[[def: Delegation (Workflow Delegation)]]

~The act by which a Workflow assigns part of its computation to another Workflow or Agent (Live), either by generating a new structure or calling an existing one. Delegation is a structural relationship, not an execution privilege.

**Deterministic Component—** A **Component** whose behavior is fully predictable: given the same inputs, it produces the same outputs. This includes deterministic code components (e.g., parsers/validators) and can include deterministic **Workflows** used as subroutines. Deterministic components are commonly used for interpretation, validation, evaluation, and action execution.

### **Deterministic Interpretation—** The principle that **Non-Agent Workflows** implement the only legal interpretations of agent media, with logic that is deterministic, testable, and certifiable,” so the same inputs yield the same actions and third parties can verify that unsafe direct mappings (e.g., from strings to destructive calls) are impossible.
