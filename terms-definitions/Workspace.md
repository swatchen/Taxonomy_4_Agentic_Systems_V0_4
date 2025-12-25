[[def: Workspace]]

~The execution environment here Workflows run and the sole location where side effects can be performed. It provides all **actuators** (tools/APIs/UI/etc.), enforces separation (agents generate; workflows interpret; workspace executes), and is the final arbiter of capability and state change. The Workspace state is the totality of the state of all its components.