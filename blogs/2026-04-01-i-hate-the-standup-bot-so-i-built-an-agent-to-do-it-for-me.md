---
title: "I hate the standup bot (so I built an agent to do it for me)"
url: "https://posthog.com/blog/standup-bot-revenge"
date: "Wed, 01 Apr 2026 00:00:00 GMT"
author: "Andy Maguire"
feed_url: "https://posthog.com/rss.xml"
---
<p>There it is. Staring at me. Sitting there. Goading me.</p><p><img alt="The standup bot, taunting me" src="https://res.cloudinary.com/dmukukwp6/image/upload/q_auto,f_auto/standup_bot_slack_hero_955e94601d.png" /></p><p>The day is nearly done. I&#x27;ve reviewed PRs, fixed bugs, hopped on quick calls, commanded my agents, and smashed that accept button all day!</p><p>And now I&#x27;m expected to fill in some stupid Slack thread with my standup updates?</p><p>Every time I manually copy-paste into our standup thread, a little part of me dies inside.</p><p>We just <a href="https://www.reddit.com/r/Futurism/comments/1jpzb8s/an_ai_model_has_officially_passed_the_turing_test/">passed the Turing test</a> and here I am copy-pasting GitHub issue URLs into a Slack thread like it&#x27;s 2010. I&#x27;m pulling up tabs, hunting for PR links, navigating all over GitHub, trying to remember what I (my agents) actually did today, and formatting it all nicely in Slack.</p><p>After a few months of this, I snapped.</p><p>I work on <a href="/docs/llm-analytics">LLM analytics</a> at PostHog. I spend my days helping people understand what their AI agents are doing. And yet here I am, manually summarizing my GitHub activity like some kind of caveman.</p><p>The irony was too much. I decided to build my own agent to beat the standup bot at its own game.</p><p>It helps too that this was also the perfect excuse to learn about and play around a little with the <a href="https://openai.github.io/openai-agents-python/">OpenAI Agents SDK</a>.</p><h2 id="the-plan">The plan</h2><p>The idea was simple:</p><ol><li>Build an AI agent that reads my GitHub activity and previous recent standups for context</li><li>Have it draft a sensible standup summary in the style I want</li><li>Chat with it to refine the draft</li><li>Post directly to Slack</li></ol><p>And because I&#x27;m a shameless dogfooder, this was the perfect excuse to:</p><ul><li>Learn the <a href="https://github.com/openai/openai-agents-python">OpenAI Agents SDK</a></li><li>Dog-food our <a href="/docs/llm-analytics/installation/openai-agents">OpenAI Agents SDK integration</a> for <a href="/docs/libraries/python"><code>posthog-python</code></a></li><li>Create something I can actually use every day</li><li>Write this very blog post</li><li>But most importantly, use AI even if it actually sometimes takes me longer each day than it did before.</li></ul><p><img alt="The perfect excuse" src="https://res.cloudinary.com/dmukukwp6/image/upload/w_280,q_auto,f_auto/tenor_1_9d61025db5.gif" /></p><p>Let me show you how it all works.</p><div class="border-l-4 border-yellow bg-yellow/10 rounded-r p-4 my-6"><strong>🌶️ Hot take</strong><p><strong>Agents are the new scripts.</strong> We used to write bash scripts and cron jobs to automate tedious tasks. Now we write agents. Scripts follow rigid rules; agents are more flexible and often that&#x27;s worth it.</p></div><h2 id="the-architecture-one-agent-to-rule-them-all">The architecture: one agent to rule them all</h2><p>I&#x27;ll be honest - I didn&#x27;t start here. My first version was a <a href="https://github.com/andrewm4894/github-standup-agent/tree/b63e51b/src/github_standup_agent/agents">three-agent system</a>: a <a href="https://github.com/andrewm4894/github-standup-agent/blob/b63e51b/src/github_standup_agent/agents/coordinator.py">Coordinator</a> that orchestrated the workflow, a <a href="https://github.com/andrewm4894/github-standup-agent/blob/b63e51b/src/github_standup_agent/agents/data_gatherer.py">Data Gatherer</a> that pulled from GitHub, and a <a href="https://github.com/andrewm4894/github-standup-agent/blob/b63e51b/src/github_standup_agent/agents/summarizer.py">Summarizer</a> that turned it all into prose. I used the OpenAI Agents SDK&#x27;s <a href="https://openai.github.io/openai-agents-python/agents-as-tools/">agents-as-tools</a> pattern so the coordinator could delegate to the others.</p><p>It was a mess. Debugging handoffs between agents was painful. The coordinator would sometimes call the wrong sub-agent, or pass incomplete context. The summarizer couldn&#x27;t see the raw tool call results - only what the coordinator decided to forward. And I needed three separate prompt templates, three model configs, and an <code>on_handoff</code> hook just to wire it all together. Way too much complexity for what is fundamentally a simple task.</p><p>So I <a href="https://github.com/andrewm4894/github-standup-agent/commit/45514bafdabd48029dfbd9519fbe54e6ea1b418f">ripped it all out</a> - deleted the coordinator, data gatherer, and summarizer, and collapsed everything into a <a href="https://github.com/andrewm4894/github-standup-agent/blob/main/src/github_standup_agent/agents/standup_agent.py">single agent</a> with all 18 <a href="https://github.com/andrewm4894/github-standup-agent/tree/main/src/github_standup_agent/tools">tools</a> directly. The result was 300 fewer lines of code and something that actually worked reliably.</p><p>The agent has various tools organized by what they do:</p><ul><li><strong>GitHub overview</strong> - activity feed and summary stats</li><li><strong>GitHub lists</strong> - PRs, issues, commits, reviews, comments (all with date filters)</li><li><strong>GitHub details</strong> - drill into specific PRs or issues</li><li><strong>Slack</strong> - fetch team standups, publish, confirm before posting</li><li><strong>Utilities</strong> - clipboard, file save</li><li><strong>Feedback</strong> - capture quality ratings and comments</li></ul><p>Here&#x27;s the actual agent definition:</p><pre><code class="language-python">def create_standup_agent(
    model: str = DEFAULT_MODEL,
    hooks: AgentHooks[StandupContext] | None = None,
    style_instructions: str | None = None,
) -&gt; Agent[StandupContext]:
    instructions = _make_dynamic_instructions(style_instructions)

    return Agent[StandupContext](
        name=&quot;Standup Agent&quot;,
        instructions=instructions,
        tools=[
            # GitHub overview tools
            get_activity_feed,
            get_activity_summary,
            # GitHub list tools (with date filters)
            list_prs,
            list_issues,
            list_commits,
            list_reviews,
            list_comments,
            # GitHub assigned items (no date filter)
            list_assigned_items,
            # GitHub detail tools
            get_pr_details,
            get_issue_details,
            # Slack tools
            get_team_slack_standups,
            publish_standup_to_slack,
            confirm_slack_publish,
            set_slack_thread,
            # Utility tools
            copy_to_clipboard,
            save_standup_to_file,
            # Feedback tools
            capture_feedback_rating,
            capture_feedback_text,
        ],
        model=model,
        model_settings=ModelSettings(),
        hooks=hooks,
    )
</code></pre><p>The agent uses <strong>dynamic instructions</strong> that inject the current standup draft, so each refinement iteration has full context without blowing the token budget. A <code>StandupContext</code> object tracks collected data across tool calls without stuffing it all into the prompt.</p><h2 id="stealing-my-own-github-activity">Stealing my own GitHub activity</h2><p>The data gathering is powered by the <code>gh</code> CLI, which means:</p><ul><li>No API tokens to manage (just <code>gh auth login</code>)</li><li>Access to everything you can see on GitHub</li><li>Real-time data, not some stale cache</li></ul><p>This approach works great for a small personal agent running locally on your machine. If you were building something multi-user or running it as a service, you&#x27;d want proper GitHub API tokens and rate limit handling - but for a daily standup helper? The <code>gh</code> CLI is perfect.</p><p>Here&#x27;s what the activity feed looks like when the agent queries it:</p><pre><code class="language-python">@function_tool
def get_activity_feed(
    ctx: RunContextWrapper[StandupContext],
    days_back: Annotated[int, &quot;Number of days to look back&quot;] = 1,
) -&gt; str:
    &quot;&quot;&quot;
    Fetch a chronological feed of all GitHub activity for the user.

    Returns a unified list of events including:
    - Commits pushed
    - Pull requests opened/merged/closed
    - Code reviews submitted
    - Issues opened/closed
    - Comments on issues and PRs
    - Branches/tags created
    &quot;&quot;&quot;
    # Uses `gh api /users/{username}/events` under the hood
    ...
</code></pre><p>The agent starts with the activity feed to get an overview, then drills down into specific PRs or issues that need more context. It&#x27;s smart about not fetching redundant data and caches results so subsequent questions don&#x27;t hit GitHub again.</p><p>Example of what the agent actually sees (from a real run):</p><pre><code>Activity Feed: 94 events in last 1 day(s)
Summary: 2 comment, 2 create, 4 issue, 13 pull_request, 26 push, 26 review, 21 review_comment

Events (newest first):
[2026-04-02 12:42] PUSH PostHog/posthog (master)
[2026-04-02 12:42] PR MERGED PostHog/posthog#53107 - feat/llma-cluster-mcp-tools
[2026-04-02 12:25] PR MERGED PostHog/posthog#53189 - feat/mcp-evaluation-test-hog-tool
[2026-04-02 12:18] REVIEW APPROVED PostHog/posthog#53175 - docs/update-convex-otel-examples
[2026-04-02 12:11] REVIEW APPROVED PostHog/posthog-js#3321 - fix/convex-remove-use-node
[2026-04-02 11:58] PR OPENED PostHog/posthog#53190 - fix/llm-prompt-oauth-auth
[2026-04-02 11:46] PR OPENED PostHog/posthog#53189 - feat/mcp-evaluation-test-hog-tool
...
</code></pre><h2 id="the-secret-sauce-posthog-llm-analytics">The secret sauce: PostHog LLM analytics</h2><p>Here&#x27;s where it gets interesting. Building an agent is one thing. Understanding what it&#x27;s actually doing? That&#x27;s where <a href="/docs/llm-analytics">PostHog LLM analytics</a> comes in.</p><p>I instrumented the entire agent with our <a href="https://github.com/PostHog/posthog-python/pull/408">OpenAI Agents SDK integration</a>. Fun fact — it was built during a one-day hackathon at a team onsite in Barcelona, where I also discovered <a href="https://en.wikipedia.org/wiki/Cal%C3%A7ot">Calçots</a> (highly recommend).</p><p><img alt="Me holding a Calçot in Barcelona" src="https://res.cloudinary.com/dmukukwp6/image/upload/q_auto,f_auto/team_calcots_295b5fff5e.jpg" /></p><p><div>That&#x27;s me holding a Calçot. This is relevant to the blog post, I promise.</div></p><p>The integration was surprisingly quick to build thanks to the <a href="https://openai.github.io/openai-agents-python/tracing/">excellent tracing support</a> that OpenAI baked into the Agents SDK. We just needed to extend their <code>TracingProcessor</code> to send spans to PostHog instead of their default backend.</p><pre><code class="language-python">from posthog import Posthog
from posthog.ai.openai_agents import instrument

posthog_client = Posthog(
    api_key=os.getenv(&quot;POSTHOG_API_KEY&quot;),
    host=&quot;https://us.i.posthog.com&quot;,
)

# This single line instruments the entire agent workflow
processor = instrument(
    client=posthog_client,
    distinct_id=github_username,
    privacy_mode=False,  # I want to see everything
    properties={&quot;app&quot;: &quot;github-standup-agent&quot;},
)
</code></pre><p>That&#x27;s it. One function call and suddenly I have full visibility into everything. (If you&#x27;re handling sensitive data, you can enable <a href="/docs/llm-analytics/privacy-mode">privacy mode</a> to exclude prompts and completions from being stored.)</p><h3 id="tracing">Tracing</h3><p>Every agent run creates a <a href="/docs/llm-analytics/traces">trace</a> with <a href="/docs/llm-analytics/spans">spans</a> for:</p><ul><li><strong>Agent execution</strong> - which agent ran and for how long</li><li><strong>Tool calls</strong> - what tools were called, with what arguments, and what they returned</li><li><strong><a href="/docs/llm-analytics/generations">LLM generations</a></strong> - prompts, completions, token usage, and latency</li></ul><p>I can see exactly how the agent decided which tools to call, what data it gathered, and how it turned all of that into prose. Each trace also gets an <a href="/docs/llm-analytics/summarization">AI-generated summary</a> so you can quickly understand what happened without digging into every span:</p><p><img alt="Trace view showing the full agent workflow with AI-generated summary" src="https://res.cloudinary.com/dmukukwp6/image/upload/q_auto,f_auto/trace_view_summary_standup_5f1559dc95.png" /></p><h3 id="prompt-management">Prompt management</h3><p>One thing I quickly realized: hardcoding prompts in source code is a pain when you&#x27;re iterating on tone and formatting. So I moved all my prompts into <a href="/docs/llm-analytics/prompts">PostHog&#x27;s prompt management</a>. The agent pulls its instructions, style guide, and context templates from PostHog at runtime:</p><ul><li><code>standup-agent-instructions</code> - the main system prompt with workflow, formatting rules, and feedback detection</li><li><code>custom-style</code> - formatting rules that enforce Slack mrkdwn links and match team conventions</li><li><code>current-standup</code> - injected dynamically with the latest draft during refinement</li><li><code>chat-context</code> - contextual template for chat mode sessions</li><li><code>generate-standup</code> - the one-shot generation prompt</li></ul><p>This means I can tweak how the agent writes standups without redeploying anything. And because PostHog tracks prompt versions, I can see exactly which version of the instructions produced which standup - useful when something goes weird.</p><p>There&#x27;s also the &quot;prompts are IP&quot; angle. You might have seen the recent <a href="https://www.reddit.com/r/ClaudeCode/comments/1s99j2t/followup_claude_codes_source_confirms_the_system/">Claude Code source map situation</a> — a packaging mishap meant the full system prompts ended up in the npm release. These things happen (shipping is hard!), but it&#x27;s a good reminder that if your prompts are in the source code, they&#x27;re one accidental publish away from being public. </p><p>For my standup agent it doesn&#x27;t matter (my half-baked vibecoded prompts are open source anyway), but if yours contain real secret sauce, a prompt store with a local cache is a nice safety net.</p><h3 id="tool-usage">Tool usage</h3><p>The <a href="/docs/llm-analytics/tools">tools tab</a> shows me which tools the agent calls most and how long they take. Turns out <code>get_pr_details</code> and <code>get_activity_feed</code> dominate - you can see <code>get_pr_details</code> right at the top with the most calls. That was actually a surprise - the agent was drilling into way more PRs than it needed to. A quick prompt tweak (&quot;only drill into PRs that look significant&quot;) cut unnecessary API calls.</p><p><img alt="Tools tab showing which tools the standup agent calls most" src="https://res.cloudinary.com/dmukukwp6/image/upload/q_auto,f_auto/tools_tab_standup_agent_73f8bd6db8.png" /></p><h3 id="clustering">Clustering</h3><p><a href="/docs/llm-analytics/clusters">Clustering</a> automatically groups similar traces together. For a personal agent like this, the clusters are pretty simple - mostly &quot;standard standup generation&quot; vs &quot;heavy iteration sessions&quot; vs &quot;quick copy-paste runs.&quot; But it&#x27;s useful for spotting patterns in how I use the agent. If you&#x27;re curious about how the clustering pipeline actually works under the hood, I wrote a <a href="/blog/llm-analytics-clustering-how-it-works">deep dive on how we built it</a>.</p><h3 id="sentiment">Sentiment</h3><p>The <a href="/docs/llm-analytics/sentiment">sentiment tab</a> is more fun. PostHog analyzes the tone of user messages, and looking at my negative ones is... humbling. Some highlights from my standup sessions:</p><ul><li>&quot;bad bad bad!!!!!&quot;</li><li>&quot;ok this is terrible im going to exit and debug - how can this happen!&quot;</li><li>&quot;you messed up the links&quot;</li><li>&quot;bad you are not following the examples or style properly&quot;</li></ul><p>In fairness, most of these are just me giving the agent iteration feedback (&quot;drop all the merged words its a bit repetitive&quot;, &quot;remove the ask, dont need it&quot;) rather than genuine rage. But sentiment analysis doesn&#x27;t know the difference, and that&#x27;s actually an interesting insight — what looks like negative user sentiment might just be a user who&#x27;s actively engaged and iterating. The positive ones are more telling: &quot;ok great stuff send that to slack&quot; and &quot;great love it lets publish to slack&quot; tend to mean the agent actually nailed it first time.</p><p><img alt="Sentiment tab showing positive and negative standup agent interactions" src="https://res.cloudinary.com/dmukukwp6/image/upload/q_auto,f_auto/sentiment_tab_standup_agent_8973d72c87.png" /></p><h3 id="evals">Evals</h3><p>Here&#x27;s where dogfooding gets real. The agent&#x27;s biggest formatting headache is Slack&#x27;s cursed <code>mrkdwn</code> syntax - it uses <code>&lt;url|text&gt;</code> instead of <code>[text](url)</code>, and the agent constantly tries to slip back into regular markdown. So I set up an <a href="/docs/llm-analytics/evaluations">evaluation</a> that automatically checks every generation: &quot;did it use correct Slack-style markdown formatting?&quot;</p><p>The eval results summary tells the whole story. Generations that pass tend to use correct Slack mrkdwn links, simple bullets, and plain text headers. The failures? The agent keeps trying to sneak in <code>**bold**</code> headers, <code>[text](url)</code> markdown links, and occasionally dumps raw JSON outputs. My favorite failure pattern: the agent would use <code>&lt;link|text&gt;</code> syntax for headings (correct for links, completely wrong for headers) — Slack doesn&#x27;t even have heading syntax, so it just needs to use plain text like <code>Did:</code>.</p><p><img alt="Eval results summary showing passing and failing patterns for Slack markdown formatting" src="https://res.cloudinary.com/dmukukwp6/image/upload/q_auto,f_auto/eval_results_summary_slack_markdown_330573b666.png" /></p><p>You can also drill into individual runs to scan each generation&#x27;s result and reasoning:</p><p><img alt="Eval runs showing individual generation results with pass/fail reasoning" src="https://res.cloudinary.com/dmukukwp6/image/upload/q_auto,f_auto/eval_runs_tab_standup_agent_4ce3da8be5.png" /></p><p>This is the kind of thing that&#x27;s nearly impossible to catch systematically without automated evals. I&#x27;d notice the odd broken link in Slack, fix the prompt, and think I&#x27;d solved it — only for a different formatting issue to pop up the next day. The eval catches all of them and shows me the patterns so I can fix the root cause in the prompt, not just whack-a-mole individual failures.</p><h3 id="user-feedback">User feedback</h3><p>This one is really just a regular <code>posthog.capture()</code> call exposed as a tool - plain old <a href="/docs/product-analytics">product analytics</a>, nothing fancy. The interesting part is that the agent decides <em>when</em> to call it. I don&#x27;t explicitly say &quot;thumbs up&quot; or &quot;thumbs down&quot; — the agent picks up on signals in my messages and fires the event itself:</p><pre><code class="language-python">@function_tool
def capture_feedback_rating(
    ctx: RunContextWrapper[StandupContext],
    rating: Literal[&quot;thumbs_up&quot;, &quot;thumbs_down&quot;],
    comment: str = &quot;&quot;,
) -&gt; str:
    &quot;&quot;&quot;Rate the quality of this standup.&quot;&quot;&quot;
    posthog.capture(
        event=&quot;$ai_metric&quot;,
        properties={
            &quot;$ai_trace_id&quot;: current_trace_id,
            &quot;$ai_metric_name&quot;: &quot;standup_quality&quot;,
            &quot;$ai_metric_value&quot;: 1 if rating == &quot;thumbs_up&quot; else 0,
            &quot;comment&quot;: comment,
        }
    )
</code></pre><p>When I say &quot;ok great stuff send that to slack&quot;, the agent infers positive feedback and captures it before publishing. When I say &quot;bad you are not following the examples&quot;, it captures negative feedback with my complaint as the comment. I never have to break out of the conversation flow to rate anything — the agent just reads the room.</p><p>That feedback is linked to the exact trace with <code>$ai_trace_id</code>, so I can go back and see what went wrong on bad runs. Over time, this builds a dataset for understanding what makes a good standup and which prompt versions produce better results.</p><h2 id="the-victory-lap-posting-to-slack">The victory lap: posting to Slack</h2><p>The final piece is actually posting to Slack. Because what&#x27;s the point of automating standup generation if I still have to copy-paste?</p><p>The agent has Slack integration with a two-step confirmation flow (because posting to your team&#x27;s Slack channel by accident would be... bad):</p><pre><code>You: ok great stuff send that to slack

Agent [Standup Agent] starting...
  Tool: capture_feedback_rating(rating=&#x27;thumbs_up&#x27;,
        comment=&#x27;User said: &quot;ok great stuff&quot; before asking to send to Slack.&#x27;)...
  Tool: publish_standup_to_slack(confirmed=False)...
Agent [Standup Agent] finished


Assistant: Captured the feedback — thanks.

Slack preview is ready (channel #team-llm-analytics, thread 1775052008.434899).
Reply &quot;yes, publish to slack&quot; (or &quot;confirm publish&quot;) and I&#x27;ll post it.

You: yes

Agent [Standup Agent] starting...
  Tool: confirm_slack_publish()...
  Tool: publish_standup_to_slack(confirmed=True)...
Agent [Standup Agent] finished

Assistant: Published to Slack in today&#x27;s standup thread.

You: ok thanks i love u

Agent [Standup Agent] starting...
  Tool: capture_feedback_rating(rating=&#x27;thumbs_up&#x27;,
        comment=&#x27;User said: &quot;ok thanks i love u&quot; after successful Slack publish.&#x27;)...
Agent [Standup Agent] finished
</code></pre><p>The agent automatically finds the latest standup thread in your configured channel and posts as a reply. No more hunting for the thread. No more copy-paste. Just vibes.</p><p><img alt="The final standup posted to Slack" src="https://res.cloudinary.com/dmukukwp6/image/upload/q_auto,f_auto/standup_bot_output_f9eff991f3.png" /></p><p>Did I save any time compared to just writing the standup myself? Debatable.</p><p>Might there be some AI slop in my updates that slipped through? Most definitely.</p><p>Did I get to use AI instead of doing it manually? Yes.</p><p>And isn&#x27;t that all that really matters?</p><div class="border-l-4 border-yellow bg-yellow/10 rounded-r p-4 my-6"><strong>🌶️ Hot take</strong><p><strong>Human in the loop still matters.</strong> Here&#x27;s the thing - as soon as I stop manually iterating and approving the standups my agent is generating, it flips into something weirdly anti-social. I&#x27;m just sending my teammates auto-generated text. Even if it&#x27;s 90% accurate and I would have approved it anyway, something feels <em>off</em> about fully automating communication with other humans. The approval step isn&#x27;t just quality control - it&#x27;s me taking ownership of my words.</p></div><h2 id="try-it-yourself">Try it yourself</h2><p>The agent is open source, <a href="https://github.com/andrewm4894/github-standup-agent">go check it out</a> or try it yourself:</p><pre><code class="language-bash"># Install
pip install github-standup-agent

# Configure
standup config

# Generate!
standup generate
</code></pre><p>You&#x27;ll need:</p><ul><li>Python 3.11+</li><li>GitHub CLI authenticated (<code>gh auth login</code>)</li><li>OpenAI API key</li><li>Optional: Slack bot token for posting</li><li>Optional: PostHog API key for analytics</li></ul><h2 id="build-your-own-instrumented-agent">Build your own instrumented agent</h2><p>The broader lesson here: if you&#x27;re building AI features, instrument them from day one.</p><p>PostHog&#x27;s <a href="/docs/llm-analytics">LLM analytics</a> gives you:</p><ul><li><strong><a href="/docs/llm-analytics/traces">Tracing</a></strong> - See exactly what your agents are doing</li><li><strong><a href="/docs/llm-analytics/calculating-costs">Cost tracking</a></strong> - Know what you&#x27;re spending on API calls</li><li><strong><a href="/docs/llm-analytics/evaluations">Evals</a></strong> - Measure quality systematically</li><li><strong><a href="/docs/llm-analytics/prompts">Prompt management</a></strong> - Version and iterate on prompts without redeploying</li><li><strong><a href="/docs/llm-analytics/clusters">Clustering</a></strong> and <strong><a href="/docs/llm-analytics/sentiment">sentiment</a></strong> - Spot patterns in how your agent is used</li><li><strong><a href="/docs/llm-analytics/collect-user-feedback">User feedback</a></strong> - Link quality signals to specific traces</li></ul><p>The OpenAI Agents SDK integration is <a href="https://github.com/PostHog/posthog-python/pull/408">available in <code>posthog-python</code></a>. Check out our other integrations for <a href="/docs/llm-analytics/installation/langchain">LangChain</a>, <a href="/docs/llm-analytics/installation/openai">OpenAI</a>, and <a href="/docs/llm-analytics/installation">many other frameworks</a>.</p><h2 id="stick-it-to-your-standup-bot">Stick it to your standup bot</h2><p>I&#x27;ve been using this agent daily for a few weeks now. It&#x27;s not perfect - sometimes it misses context or formats things weirdly. But that&#x27;s the beauty of having full observability: I can see exactly what went wrong and iterate.</p><div class="border-l-4 border-yellow bg-yellow/10 rounded-r p-4 my-6"><strong>🌶️ Hot take</strong><p><strong>Why are we even doing standups?</strong> Honestly, summarization as a category feels crazy early. The fact that I have to manually aggregate my work across GitHub, Slack, Linear, and Google Docs is absurd. I should just be able to read the &quot;headlines&quot; of my work life each morning - a personalized digest of what matters, generated automatically from all my tools. No one has solved this yet. Maybe standups are just a symptom of our tools not talking to each other. The standup bot isn&#x27;t the enemy - it&#x27;s a coping mechanism for a fragmented toolchain.</p></div><p>The standup bot still messages me at 6 PM. But now I just smile, run <code>standup generate</code>, and let my agent do the talking.</p><p>F*$k you, standup bot. I win.</p><h2 id="further-reading">Further reading</h2><ul><li><a href="/blog/optimizing-agent-cost">How we caught our AI agent embezzling tokens</a> - Lessons on tracking and optimizing agent costs</li><li><a href="/blog/llm-analytics-clustering-how-it-works">How we built automatic clustering for LLM traces</a> - Deep dive into the clustering pipeline</li><li><a href="/blog/8-learnings-from-1-year-of-agents-posthog-ai">8 learnings from 1 year of agents</a> - What we&#x27;ve learned building AI at PostHog</li></ul><div tabindex="-1"><ol class="fixed bottom-4 right-4 z-50 flex flex-col gap-2 sm:w-[390px] max-w-[calc(100vw_-_2rem)] m-0 list-none outline-none" tabindex="-1"></ol></div>
