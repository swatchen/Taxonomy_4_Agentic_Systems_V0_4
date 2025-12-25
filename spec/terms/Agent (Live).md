[[def: Agent (Live)]]

Any instance of a Workload executing an Agent Workflow (Agentflow) that is architecturally forbidden from executing actions**\*** and can only produce generated media; it is “a stateful, goal-oriented generator defined by its perspective over the duration of its workload.” (“Agent,” “Live Agent,” and “Agent (Live)” are synonymous in T4AS.)
**\***Calls to data fetching tools that operate only in the Workspace may be made from within the Agent’s Agentflow )

**Agent Role ** - The storehouse and management layer for persistent perspectives that can be instantiated as Agents (Live). An Agent Role’s functions are split between one or more Workflows. An Agent Role is defined as the Sub-Workspace where Workflows relevant to that Role run, and where Workload Execution Records are accumulated. It does not have its own perspective; instead, it is an addressable reservoir of context that can be assembled into temporary perspectives for immediate tasks. The Agent Role provides the illusion of a continuous, persistent Agent to the user, even if the underlying Live Agents are ephemeral.
