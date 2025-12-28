## **Taxonomy for Agent Systems (T4AS, “tee-four-az”)**

[**stephen.vitka@gmail.com**](mailto:stephen.vitka@gmail.com)                                                **V0.4              December’ 25**



## **Abstract**

This taxonomy establishes a precise and implementation-agnostic vocabulary for describing *any* agentic system. As agent frameworks proliferate and the term “agent” is used inconsistently across industry, this document isolates the fundamental architectural primitives required for clarity, safety, and interoperability. T4AS begins by delineating five core concepts—**Model, Workspace, Workflow, Agent (Live), and Agent Role**—before presenting recommended implementations such as **Ephemeral Agents**, **Unified Workflow Lifecycle Documents**, and **token-based OCAP** (introduced in a later, implementation-focused Appendix).

In this taxonomy, the term **Agent** is equated with a **Live Agent**. A Live Agent is any instance of a Workload executing an Agent Workflow that is constrained to generation-only behavior. The persistent “persona” that a user experiences is instead located in an **Agent Role**: a storehouse and management layer for persistent perspectives and histories that can be instantiated as Live Agents, which serve their term in the Role, archive their function, and then die. The Agent Role can do things at the level of system behavior and appears to have agency, but it is the *illusion of persistence* constructed from many short-lived Live Agents and their Workload Execution Records. Agent Roles do not think; rather, they supply the contextual “slice” from which Live Agents are instantiated, archived, and replaced.

T4AS is intentionally descriptive rather than prescriptive: it defines the universal parts of any agentic system, independently of execution environment or design philosophy. Recommended implementations are provided after the taxonomy itself. While users, product teams, and public discourse will inevitably continue to use the word “agent” imprecisely, **system architects and developers should adopt precise use of the T4AS vocabulary** to ensure that agentic systems remain understandable, composable, auditable, and safe.



## **Core Definitions in This Taxonomy**

**Model**

   A foundational, often opaque computational block that generates outputs from inputs; typically trained using methods like gradient descent or reinforcement learning. A probabilistic engine that generates multi-media output. *Example: Llama 3 (8B parameters) is a Model.*

* **Workspace**

   Defined as the place where workflows that use models happen, the Workspace provides a Workflow with capabilities to act on it. It hosts the actuators (tools, APIs, microservices, UI) and enforces the security boundary between generation and execution, so that state changes originate from deterministic interpretation of an agent’s generated media.

* **Workflow**

   The most general term for any defined sequence of AI-driven operations. A Workflow utilizes one or more Models, Agents, and deterministic Components; when executed, it becomes a **Workload** (a running or paused instance with state) and produces a **Workload Execution Record** (the immutable, auditable artifact of that execution).

* **Agent (Live)**

   Any instance of a Workload executing an **Agent Workflow (or Agentflow)** that is architecturally forbidden from executing actions and can only produce generated media. An Agent (Live) is a stateful, goal-oriented generator defined by its perspective over the duration of its workload. In practice, “Agent,” “Live Agent,” and “Agent (Live)” are used synonymously in this document.

* **Agent Role**

  The storehouse and management layer for persistent perspectives that can be instantiated as Agents (Live). An Agent Role’s functions are split between one or more workflows. An Agent Role is defined as the sub-workspace where workflows relevant to that Role run, and where Workload Execution Records are accumulated. It does not have its own perspective; instead, it is an addressable reservoir of context that can be assembled into temporary perspectives for immediate tasks. The Agent Role provides the illusion of a continuous, persistent Agent to the user, even if the underlying Live Agents are ephemeral.

## **1. Introduction & Rationale** 

## **([extended version](https://docs.google.com/document/d/1QARklBNiZmVX1twwjsbZMvqjpj4JInS2Fe9F0QujaN4/edit?usp=sharing) & [“fun” metaphor](https://docs.google.com/document/u/0/d/1Q3kFmnFw8r_5F_SCvNPy9OdWpYceDxUANMqaYN1lURo/edit))**

This taxonomy is designed to disambiguate the use of terms for key components of AI Systems. We cannot talk about a thing unless we first delimit what we are talking about. By dividing an AI System into an **Architectural (Safety) Triad**, we allow there to be security layers between these components (e.g., verifying systems and passkeys), and many failure modes to require coincidental failure of all three components.  

1. **The Agent (Generator)**: An agent is a stateful, goal-oriented system defined by its perspective. Critically, an agent is architecturally forbidden from executing actions; having access only to research tools, at most. Its only allowed output is “generated media” (e.g., text, code, or images). It is confined to being an “oracle” that can only generate plans or requests.

2. **The Non-Agent Workflow (Orchestrator)**: This is the “recipe” or “process” that contains the actual execution logic. This workflow calls the Agent to get generated media, interprets that media, and then calls tools to perform actions.

3. **The Workspace (Environment)**: This is the “place” where execution happens. The Workspace provides the certified “actuators”—the tools, APIs, microservices, and user interface components—that the workflow can use. 

This Triad is the core safety structure of this taxonomy. All of the higher-level constructs in T4AS – Agent Roles, Workload Execution Records, certification regimes, and multi-workspace layouts – are layered on top of it.

The field of artificial intelligence is undergoing a period of explosive, almost biological, diversification. This “Cambrian Explosion” of AI capabilities, driven by powerful foundation models, has given rise to a new class of computational entity: the autonomous agent. We are rapidly moving beyond simple generative tools to complex, autonomous systems capable of perceiving, reasoning, planning, and acting in open-ended environments. This transition marks a fundamental paradigm shift from systems that merely perform computation to systems that exhibit behavior.

However, this rapid evolutionary leap has occurred in the absence of a corresponding evolution in architectural discipline. The result is a vibrant but chaotic ecosystem characterized by immense potential but also by profound structural weaknesses. The rush to leverage the power of Large Language Models (LLMs) has prioritized rapid feature development over the establishment of stable, secure architectural foundations. This ad-hoc development is no longer tenable. The accumulation of “architectural debt”—the implied cost of rework caused by choosing easy solutions now over better, more robust approaches—poses a significant and growing threat to the security, reliability, and scalability of these powerful technologies.

This architectural chaos has a direct and severe consequence: a widening and increasingly exploited attack surface. The lack of clearly defined and enforced boundaries between an agent’s reasoning core, its tools, and its data sources creates systemic vulnerabilities. Threats such as indirect prompt injection, tool misuse, and malicious multi-agent collusion are not isolated bugs but emergent properties of ill-defined and dangerously coupled systems. In the world of agentic AI, architectural ambiguity is an active attack vector.

The current landscape of agent orchestration frameworks like LangChain, AutoGen, and CrewAI exemplifies this fragmentation. While powerful, they are built on fundamentally different and often conflicting architectural philosophies, creating a “Tower of Babel” scenario where core concepts like state, memory, and communication are inconsistently defined. This hinders interoperability, creates ecosystem lock-in, and makes the application of universal security and governance standards an intractable problem.

To move from this state of unstructured complexity to disciplined engineering, a formal, disambiguated architectural taxonomy is not merely beneficial; it is an urgent necessity\! As AI workflows become increasingly complex and interwoven, a clear and precise vocabulary is needed to distinguish their various parts and functions. This taxonomy provides a disambiguated framework for classifying AI components to support critical use cases, including:

* **Access Control:** Defining and enforcing permissions for different components and agents.

* **Indexing & Filtering:** Enabling the discovery and organization of workflows based on their structure and purpose.

* **Certification:** Creating a basis for verifying the security, reliability, and provenance of agents, workflows, and workspaces, along with their components (with attention to embeddedness; see Section 3.3).

* **Componentized Composability:** Facilitating the assembly of complex systems from trusted, reusable parts.

This document outlines a proposed set of definitions to bring clarity and precision to the architecture of modern agentic systems, establishing the common ground necessary for a mature and secure engineering discipline to emerge.



## **2. Primitives in Depth**

### **2.1 Model**

A **Model** is the static set of learned parameters—such as weights, biases, token embeddings, and other configuration artifacts—that define a generative function. In its stored form, a Model is inert data and cannot execute or generate anything.

A **Loaded Model** is a Model whose parameters have been instantiated in memory (e.g., loaded into RAM). Even when loaded, it remains passive: it has no context, no goals, no perspective, and no ability to act.

A Model becomes part of a Live Agent only when a Workflow injects context into a Loaded Model to perform a specific generative task. This combination—Loaded Model plus contextual input—constitutes the generative core of a Live Agent, but the Model itself is defined as never possessing state or agency.

### **2.2 Workspace**

A **Workspace** is the execution environment in which Workflows run and state changes occur. It is the “place” where computation happens and the sole location where side effects—such as writing files, calling APIs, updating data, or interacting with users—can be performed.

The Workspace provides all **actuators**: tools, APIs, microservices, UI elements, and any other capability that can alter state. Workflows may call these actuators, but Live Agents cannot. Live Agents may only generate media; they never directly perform actions.

To ensure this separation, the Workspace enforces the core security triad of T4AS:

* **Live Agents generate output only.**

* **Workflows interpret that output deterministically.**

* **The Workspace executes the resulting actions through its actuators.**

A Workspace may contain multiple Workflows, multiple Agent Roles, and any number of Live Agent instantiations, but it is always the final arbiter of capability. No state changes can occur except through its controlled actuators and deterministic logic.

The Workspace also provides the mechanisms to construct and inject context into Loaded Models, enabling them to become Live Agents for the duration of a task. When the task ends, the Workspace terminates the Live Agent and retains only the resulting Workload Execution Record.

### **2.3 Workflow**

A **Workflow** is a defined sequence of steps describing how computation proceeds. It is the deterministic program that structures how Models, Live Agents, and other components are invoked. A Workflow contains the execution logic of a system: it decides what to do, when to do it, and which components to call.

A Workflow becomes meaningful only when executed within a Workspace. During execution, a Workflow can:

* Instantiate new Live Agents by loading a Model into memory and injecting the context needed for the task.

* Interact with existing Live Agents, including those created earlier or running independently.

* Interpret the generated media produced by Live Agents.

* Invoke actuators within the Workspace to update state or perform actions.

* Call other Workflows or re-enter itself using well-defined inputs and outputs.

A Workflow does not “think.” It **orchestrates**: coordinating generative components, deterministic components, and Workspace actuators while maintaining strict separation between generation and execution.

**2.3.1 Sub-Types of Workflows**

*  **Deterministic Workflows**—These Workflows contain no generative components. Their behavior is fully predictable: given the same inputs, they always produce the same outputs. Deterministic Workflows often serve as interpreters, validators, evaluators, or action-executors within the Workspace.

*  **Agent Workflows (Agentflows)—**These Workflows define the behavior of a Live Agent. Their distinguishing characteristic is that they are **output-only**: they may generate media, call other generative components, or manage an internal perspective, but they cannot perform actions. Executing an Agent Workflow creates a Live Agent.

*  **Composite Workflows—** A Workflow that invokes other Workflows—deterministic, generative, or both—as part of its operation. Composite Workflows are the basis for hierarchical or multi-stage processes, including orchestrators that coordinate multiple Live Agents.

*  **Reactive Workflows—**A Workflow  triggered by events rather than direct user or system calls. It may respond to changes in the Workspace, external stimuli, or scheduled triggers. Reactive Workflows can instantiate Live Agents or other Workloads as part of their event handling.

### **2.3.2 Lifecycle of a Workflow**

A Workflow proceeds through three canonical states:

1. **Workflow (Pre-Execution)**

    The declarative plan or code. It defines the complete sequence of operations, dependencies, and structure of execution.

2. **Workload (Executing State)**

    A **Workload** is a running or paused instance of a Workflow. It holds the live execution state, which may include:

   * the current step,

   * memory or intermediate data,

   * references to Agents or Actuators it has called,

   * and the temporary contexts and variable values associated with those calls.

3. **Workload Execution Record (Post-Execution)**

    An immutable record of the completed run. It contains final outputs, key internal steps, state transitions, and the generative media produced by Live Agents. This record forms the durable history accessible to other Workflows or Agent Roles.

### **2.3.3 Sub-Types of Workloads**

* **Agent Workloads (Live Agents)** – A Live Agent is a Workload executing an **Agent Workflow** (see Section 2.4).

* **Paused Workloads** – A Workload whose execution has been suspended. Sufficient data is retained to rebuild state such that it may resume at a later time, materially unchanged.

* **Remote Workloads** – A Workload executing in a different Workspace, device, or environment, but still part of the same logical Workflow invocation chain.



### **2.4 Agent (Live)**

An **Agent (Live)**—referred to simply as an *Agent* in this taxonomy—is any Workload executing an **Agent Workflow**, which is a Workflow constrained to generation-only behavior. An Agent produces generated media such as text, code, or structured data, and cannot execute actions or directly alter state. This makes all Agents in this taxonomy “Oracle” Agents, the obviously safer architecture recommended since the beginning by AI Safety researchers. All actions are performed only by Workflows interpreting the Agent’s output within a Workspace.

A Live Agent is formed when:

1. A Model is loaded into memory, and

2. A supervisor Workload injects the context necessary to create an Agent Workload that can begin producing output.

Once running, a Live Agent is a generative process with its own internal run state for the duration of its Workload. This run state constitutes the Agent’s **perspective**: its evolving internal context, temporary memory, and task-oriented focus. The perspective lasts only as long as the Workload is running, and is not itself persistent across instances unless deliberately reinstantiated.

**A Live Agent**:

* is a running generative (non-deterministic) process,

* may be called by other Workloads,

* may persist across multiple calls to it or be erased after each,

* may be able to evolve future instances through altering its own Workflow logic.

#### **Hierarchy and Composition**

A Live Agent contains embedded generative components, including Models or other Agent Workloads. When invoked, these components function as sub-agents within the Live Agent’s perspective. This allows Agents to be composed hierarchically while preserving the generation-only constraint.

#### **Agent vs. Model**

A Model becomes part of an Agent only when loaded and provided with context for a specific task. A Live Agent is therefore not just a Model but a Model plus context plus a running Agent Workflow. The Agent is defined by its process and perspective, not by its parameters alone.

### **2.5 Agent Role**

An **Agent Role** is the persistent, addressable context that defines an agentic identity within a system. Unlike a Live Agent—which is a running generative Workload—an Agent Role does not run, generate media, or execute any behavior. It is a structural entity that stores the long-term information, histories, and configuration that enable multiple Live Agents to appear coherent and continuous over time.

**An Agent Role contains:**

- accumulated **Workload Execution Records**,

- structured knowledge bases,

- user preferences and behavior guidelines,

- long-term goals or objectives,

- and any other persistent data that informs how future Live Agents should behave.


**Agent Roles support a wide range of Live Agents:**

- a single long-lived Live Agent,

- recurring re-instantiations over time (the preferred practice; see later discussion of Ephemeral Agents),

- multiple concurrent Live Agents,

- or any combination thereof.

An Agent Role has **no perspective** of its own. It does not think or generate content. Instead, it serves as a repository from which a Workflow can assemble the specific context needed to instantiate a Live Agent for a given task.

#### **Role as Persistent Identity**

While a Live Agent exists only as long as its Workload is active, an Agent Role provides the continuity of identity across many such instances. It defines how a newly created agent “remembers,” how it presents itself, and how successive ones maintain consistent behavior across time. Multiple Workflows may reference the same Agent Role, allowing it to act as a shared, persistent persona within the system. The world at large will address this identity (utilizing its associated identifiers), treating it as a coherent thing that can be trusted to act within certain boundaries—or not trusted, due to a poor record.

#### **Context Assembly and Reinstantiation**

When a Workflow needs generative behavior from an agent, it uses the Agent Role to:

1. **Select and assemble relevant context**,

2. **Inject this context into a Loaded Model**,

3. **Create a Live Agent** with the appropriate temporary perspective,

4. **Interpret the Live Agent’s output**, and

5. **Store the resulting history** back into the Agent Role.

In this way, the Agent Role is the locus of long-term structure, while the Live Agent is the short-term generative process.

#### **Role as Structural Sub-Workspace**

An Agent Role should be implemented as a distinct sub-workspace containing all data, workflows, and historical material associated with that Role. The Role also defines access to external systems and which external systems are allowed access and on what terms. This organizational structure ensures clear boundaries between the persistent knowledge of the Role and the transient computation of the Live Agents instantiated from it. 

Agent Roles may themselves be **nested**: a Role’s sub-workspace can contain more specialized, subordinate Roles that share the same principal but enforce narrower responsibilities or perspectives. This taxonomy discusses this architectural pattern of **Nested Agent Roles** in more detail in Section 4.2.3



### **3. Taxonomy Built for Security \+ Auditability**

At the heart of T4AS is the **Architectural Triad**:

1. **Agent (Live)** – the **Generator**

2. **Non-Agent Workflow** – the **Orchestrator**

3. **Workspace** – the **Environment**

This triad is the **core security and composition model** of the taxonomy. It enforces a strict separation between **generation**, **interpretation**, and **execution**, and all higher-level constructs—**Agent Roles**, **Workload Execution Records**, and certification regimes—are layered on top of this separation rather than weakening it (see also Section 3.3 on embeddedness and certification scope).

**Live Agents generate media; they never act.**

A Live Agent (an Agent (Live) in this document) is any Workload executing an **Agent Workflow** that produces only generated media (tokens that decode to text, code, images, or other media). It cannot directly call tools, mutate state, or trigger external side effects. It is a constrained **oracle**.

**Non-Agent Workflows interpret media and decide what to do.**

A Non-Agent Workflow is the deterministic **program** that:

* invokes Live Agents,

* interprets their generated media,

* and decides which actions (if any) should be taken.

It is the **only place where agent output is given meaning**: the Workflow parses, validates, filters, and transforms the Agent’s media into explicit, auditable calls to actuators.

**The Workspace executes actions and enforces boundaries.**

The Workspace is the **only locus of side effects**. It provides:

* certified **actuators** (tools, APIs, microservices, UI elements),

* **policy enforcement** and capability boundaries,

* and the mechanisms for recording every executed action into **Workload Execution Records**.

Non-Agent Workflows can only act by routing requests through the Workspace; Live Agents cannot act at all.



#### **3.1 How the Triad Yields Security and Auditability**

The Architectural Triad supports security and auditability through three core invariants:

1. **Generation is separated from execution.**

   * Live Agents are maximally expressive but **incapable of acting**.

   * They can only issue *proposals* (media) that must be interpreted.

2. **Interpretation is deterministic and reviewable.**

   * Non-Agent Workflows implement the **only** legal interpretations of agent media.

   * Their logic is deterministic, testable, and certifiable: given the same inputs, the same actions result.

   * This allows third parties to verify that, for example, “no generated string can ever directly become a delete\_all\_files() call.”

3. **All side effects are mediated and recorded in the Workspace.**

   * Every state change must pass through Workspace actuators.

   * The Workspace logs these actions into **Workload Execution Records**, creating a durable trail that other Workflows and Agent Roles can inspect.

   * This enables **post-hoc forensics**, **compliance checks**, and **reputational scoring** for Agent Roles and Workflows.



#### **3.2 Role of Agent Roles in the Triad**

**Agent Roles** sit **inside the Workspace** as structured sub-workspaces:

* They accumulate **Workload Execution Records**, curated knowledge, and configuration.

* They **do not break the triad**: Agent Roles never execute actions or generate media themselves.

* Instead, they provide the **persistent context** from which new Live Agents are instantiated and into which their histories are archived.

From a security standpoint, Agent Roles strengthen the model:

* They make long-term behavior and capabilities **inspectable** at the level of the Role rather than any single Live Agent instance.

* They provide a natural locus for **policy enforcement**: which Live Agents may be instantiated, with what context, and with what downstream workflows.


#### **3.3 Analogy: Strategist, General, and Battlefield**

A familiar analogy captures the triad:

* The **Agent (Live)** is a brilliant **strategist** who can only produce a written battle plan. They cannot speak to troops or fire a weapon.

* The **Non-Agent Workflow** is the **General** in the command tent who reads the plan, interprets it, and issues specific, concrete orders.

* The **Workspace** is the **battlefield** itself: the troops, logistics, communication systems, weapons, and physics, itself. 

The strategist’s ideas may be powerful, but **nothing moves** until the General and the battlefield infrastructure agree on what to do and how to do it. And every order issued on the battlefield is logged for later review.



#### **3.4  Example: Cryptographic Signing**

A critical example of this separation is **cryptographic signing**:

* A Live Agent **cannot sign** its own actions. It never directly performs them.

* Instead, the Agent may generate a **request**: “Sign transaction X on behalf of Role Y under Policy Z.”

* A Non-Agent Workflow with the appropriate permissions **parses this request**, checks it against policy, and decides whether it is valid.

* If valid, the Workflow calls a **signing actuator** provided by the Workspace, which performs the cryptographic operation using the appropriate keys and produces an auditable record.

In this pattern, **trust** lives in:

* the **Workspace** (that keys are protected and actuators behave correctly), and

* the **Non-Agent Workflow** (that it interprets Agent media in a safe, policy-compliant way),

not in the opaque internal state of a Model or Live Agent. This is the central security promise of T4AS: **Agent creativity is always separated from the power to act.**



## **4. Architectural Blueprints: Components, Frameworks and “Embeddedness”**

Agents (Live), Workflows, and Agent Roles are constructed from smaller, reusable parts (**Components**) according to specific architectural plans (**Frameworks**). Components give us **atomic, certifiable building blocks**; frameworks describe **how those blocks are wired together** inside Workflows, Live Agents, and Roles.

### **4.1 Component**

A **Component** is a self-contained, reusable, and often certifiable part of a larger system.

Components can be:

* a deterministic piece of code (e.g., a data parser, a JSON schema validator, a policy checker),

* a specific **Model**,

* a simple **Workflow**,

* or even a well-scoped **Agent Workflow** used as an oracle inside other systems.

Components are the **building blocks** referenced by Frameworks and Workflows and can be independently certified (especially when they are low-embeddedness components; see Section 4.3). Certification can require certification of  sub-components, to an acceptable level of granularity, or be dependent on the exact context and use of the component (for high embeddedness components)

Within T4AS:

* A **Model** is a particular kind of Component (a probabilistic generator).

* A **deterministic Workflow** can be treated as a Component when it is invoked as a subroutine.

* An **Agent Workflow** can also be treated as a Component when it is embedded as a callable oracle inside other Workflows.

What *doesn’t* change is the security boundary: a Component is never trusted simply because it is “inside” an Agent. Its capabilities are always mediated by the **Non-Agent Workflows** and the **Workspace** that call it.

### **4.2 Framework**

A **Framework** is a recipe or architecture for composing heterogeneous Components into a larger system. It describes:

- which Components are used,

- how they are connected,

- which data and capabilities flow between them,

- and which **Workflows** and **Agent Roles** they serve.

T4AS distinguishes several important sub-types.



#### **4.2.1 Workflow as Framework**

A Workflow’s definition can be viewed as a **Framework** that orchestrates Models and Components in a sequence (or more complex control structure):

- It specifies the **order** of operations and decision points.

- It defines how **Live Agents** are instantiated, invoked, and terminated.

- It encodes the **deterministic interpretation** of generated media and maps that interpretation to Workspace actuators.

In this sense, a Workflow is both:

- a **runnable program** (when executed as a Workload), and

- a **framework specification** for how its Components must interact.

The extent to which a Workflow can be reused or globally certified often depends on its embeddedness in particular Roles and Workspaces (see Section 3.3).



#### **4.2.2 Agent Workflow (as Framework)**

An **Agent Workflow** defines the core architecture and purpose of a specific class of **Agent (Live)** in its **pre-execution** form.

- It contains the deterministic logic that governs the Agent’s operation and how it utilizes its other Components. **But those components are not specified by the framework.**

- It describes *how* an Agent’s perspective is constructed and updated during a run, without binding to any particular Model, toolset, or datastore.

- It establishes the internal structure of prompts, tools for *reading* data, and any internal evaluation or self-critique loops that remain within the generation-only boundary.

In other words, the Agent Workflow is an **abstract pattern**. It defines:

- the **control flow** of an Agent (Live),

- the **interfaces** that Components must implement,

- and the **constraints** under which those Components may be used,

while remaining agnostic about **which** concrete Components are actually chosen. Those concrete Components (specific Models, readers, critics, etc.) are supplied later by:

- Non-Agent Workflows that instantiate the Agent, and

- the Agent Role / Role Framework that assembles context and configuration.

Crucially, an Agent Workflow:

- **never** contains actuators that can directly change Workspace state,

- preserves the generation-only nature of Agents (Live), and

- is built to be reusable: many different Live Agents can be instantiated from the same Agent Workflow with different Components, Workspaces, and Agent Roles.

From a certification standpoint, an Agent Workflow is a **key certifiable Component**—usually with relatively high required embeddedness, because it should be tightly coupled to particular Roles and Workspaces (see Section 3.3). Once verified, any Live Agent instantiated from a conforming implementation can inherit those guarantees, as long as the surrounding Workflows and Workspace respect the T4AS triad.



#### **4.2.3 Role Framework**

A **Role Framework** defines how an Agent Role structures and manages its persistent context:

- how **Workload Execution Records** are stored, indexed, and pruned,

- how **long-term knowledge** and preferences are organized,

- how context is **assembled** for new Live Agents (e.g., which histories, which documents, which policies),

- and how external systems (registries, identity layers, governance services) may be referenced.

A Role Framework is not itself a generator and does not run as a Live Agent. Instead, it:

- defines the **sub-workspace structure** of the Role,

- specifies which Workflows may read or write which parts of the Role’s context,

- and acts as the **contract** that makes Agent Roles inspectable, composable, and certifiable entities in larger systems (again, with embeddedness shaping what can be certified globally versus only in context; see Section 3.3)

#### **4.2.3.1 Nested Role Frameworks**

A Role Framework can define not just a single flat sub-workspace, but a **hierarchy of sub-workspaces** corresponding to **Nested Agent Roles**. In this pattern, one Agent Role’s sub-workspace contains other Agent Roles that share the same principal but embody more specialized perspectives, responsibilities, or governance surfaces. Each nested Role is still a standard Agent Role—persistent, non-generative, and implemented as a sub-workspace—but it is **embedded within** a broader Role Framework.

Structurally:

The **parent Role** (for example, a person’s long-lived **Digital Twin Role**) defines the broad identity, life-long history, and high-level policy surface for that principal.

One or more **nested Roles** live inside that parent Role’s sub-workspace (for example, a specialized **Tutor Role**, a **Health-Advisor Role**, or a **Financial-Steward Role**). Each:

  - has its own Role identifier and catalog entry,

  - maintains its own Workload Execution Records and configuration,

  - and is governed by a Role Framework that constrains which Workflows and Live Agents may operate “under that hat.”

The parent Role’s Framework thus becomes a **framework-of-frameworks**: it defines which nested Roles exist, how they share or partition the parent’s data and policies, and how nested Roles may call one another or coordinate within the same principal’s overall identity.

This is a **high-embeddedness** pattern (see Section 4.3). A nested Role’s behavior and risk profile depend strongly on:

- the **parent Role** whose histories and policies it reads from,

- the **Workspace** where the larger Digital Twin is installed,

- and the **Workflows** that are allowed to assemble context from the parent into the nested Role’s sub-workspace.

Certification and governance often need to treat the parent and its nested Roles as a coupled unit, even if each nested Role carries its own catalog entry and versioning.


#### **Example: Tutor Role nested inside a Digital Twin Role**

Consider a student’s **Digital Twin Role** as the broad, long-lived identity that accumulates the student’s comprehensive history: coursework, problem attempts, explanations received, timing and ordering of concepts, and past tutoring sessions. Nested within this Role, the system may define a **Tutor Role** whose sole purpose is to help that same student learn more effectively.

The Tutor Role’s Workspace:

- **indexes and re-frames** the parent Role’s Workload Execution Records related to learning a domain,

- extracts patterns in **how this particular student actually learned** various topics—what explanations worked, which examples unblocked them, which practice schedules stuck,

- and encodes policies about acceptable pedagogy, pacing, and assessment for this principal.

When a Workflow instantiates a Live Agent from the Tutor Role, it assembles context that could **pretend to be** “a version of this student who has already mastered the material,” looking back along their own learning trajectory. The resulting Tutor-Agent (a Live Agent tied to the Tutor Role) then:

- proposes teaching plans and explanations that mirror the strategies that worked in the student’s own history,

- surfaces examples and analogies that previously produced breakthroughs,

- and adapts its style and pacing to match the student’s recorded preferences and prior success patterns.

From the student’s point of view, this feels like a future, more-knowledgeable version of their Digital Twin “traveling back in time” to teach their earlier self. Architecturally, however, nothing supernatural is happening: the system is simply instantiating a Live Agent from a **highly embedded Tutor Role**, whose Framework is tuned to reuse the parent Digital Twin Role’s histories as a model of “how you, specifically, learned this.”

Because the Tutor Role is nested inside the Digital Twin Role:

- its correctness and safety must often be evaluated **in the context of that particular Digital Twin Framework and Workspace**, and

- certifications and governance policies may need to attach jointly to “Digital Twin Role \+ Tutor Role \+ installed Workspace,” rather than treating the Tutor Role as a globally reusable, low-embeddedness component.

Nested Role Frameworks thus provide a way to factor a complex identity into **role-scoped sub-identities**—each with its own policies and certification surface—while still grounding all of them in a single principal’s Digital Twin and a single installed Workspace.



#### **4.2.4 Security-Centered Framework Design**

Although T4AS is descriptive, **Frameworks should be built with security at their core**. In particular:

* Frameworks for Workflows, Live Agents, and Roles should use principles such as **Object-Capability (OCAP)** so that internal Components can only make properly provenanced calls. (see Appendix A)

* Capability boundaries should remain aligned with the **Architectural Triad**:

  - generation constrained to **Agents (Live)**,

  - interpretation constrained to **Non-Agent Workflows**,

  - execution and state changes constrained to the **Workspace** actuators.

This creates “firewalls” that enhance security even between Components inside a single Agent or Role. A certified **Agent Workflow** (e.g., a hypothetical *“Public Representative Agent Workflow v2.1”*) is therefore both:

- a **Component** in its own right (subject to certification), and

- the blueprint from which many concrete Live Agents can be safely composed and instantiated.

The appropriate **scope** of such certification depends on the component’s embeddedness (see Section 4.3): Components with  low-embeddedness (few embedding requirements)  allow broad use under a single certification, whereas one with higher embeddedness ones must often be evaluated in the context of particular Roles and Workspaces.

### **4.3 Embeddedness, the Degree of Context Dependency** 

Every Component  in T4AS (including Agent Workflows and Role Frameworks) can be characterized by its **embeddedness**:

**Embeddedness** is the degree to which a Component or Framework derives its behavior, permissions, or meaning from the **context that contains it**—its Agent Role, Workspace, parent Workflow, or other enclosing structures. It is the degree that a component must be embedded within context to function as properly. 

* A **low-embeddedness** artifact behaves **similarly across many contexts**.

  * Its behavior and risk profile are largely determined by its own internal logic.

  * Examples include:

    - a pure deterministic parser Component,

    - a simple validation Workflow used as a subroutine,

    - or a Model that is always wrapped in the same narrow, well-understood calling pattern.

  * Such artifacts are natural candidates for **global certification and reuse**: once verified, they can be incorporated into many architectures with minimal additional analysis.

* A **high-embeddedness** artifact has behavior and risk that depend strongly on its **embedding context**:

  - the Agent Role whose data and policies it reads from,

  - the Workspace whose actuators and access controls it relies on,

  - or the parent Workflow that constructs its prompts and interprets its outputs.

  - For these artifacts, safety and semantics **cannot be evaluated in isolation**; they make sense only as part of a larger architectural assembly.

Embeddedness therefore shapes **certification scope**:

* **Low-embeddedness Components and Frameworks** can often be:

  - certified once,

  - versioned and catalogued,

  - and reused across multiple systems with relatively stable guarantees.

  
* **High-embeddedness Components and Frameworks** are better treated as **contextual constructions**:

  - they may need to be certified together with the Agent Roles, Workflows, and Workspaces they are embedded in,

  - and their behavior may change meaningfully when that embedding changes, even if their internal logic does not.

T4AS does **not** prescribe a particular metric or threshold for embeddedness (required embedding).  Instead, it provides:

- a vocabulary for architects and governance bodies to say things like

   “This Role-specific Agent Workflow is highly embedded in Role R and Workspace W; this parser Component has low-embeddedness and can be certified as a reusable library.”

- a conceptual handle for distinguishing:

  - Components and Frameworks that can safely carry **global certifications**, from

  - those that require **context-specific analysis**.

## **5. Generated vs. Called Workflows**

In T4AS, Workflows and Agents (Live) rarely operate in isolation. Instead, they often **delegate** work to other Workflows and Agents. This delegation happens in two structurally distinct ways:

1. **Generated** – a Workflow produces a *new* Workflow (or Agent Workflow) definition as data.

2. **Called** – a Workflow invokes a *pre-existing* Workflow or Agent via a stable interface.

This distinction matters for **provenance, trust, and composability**. It tells us whether we are dealing with:

- a newly created, tightly coupled **sub-structure** (generated), or

- an existing, independently managed **peer or dependency** (called).

### **5.1 Generated Workflows and Sub-Agents**

**Generated Workflows** are created on the fly by a parent Workflow for a specific purpose. Conceptually, the parent:

1. **Generates a new definition** (as structured media) describing a Workflow or Agent Workflow suited to a novel task.

2. Optionally **records** where this generated definition came from (its origin Workflow, Agent Role, configuration, etc.).

3. May then **execute** this newly defined Workflow as a Workload within a Workspace.

Two common generated sub-structures are:

* **Generated Sub-Workflow**

   A new, temporary **Workflow definition** produced for a novel or specialized task. For example, a planning Workflow might generate a one-off validation Workflow to check a particular class of outputs. Structurally, this is still “just” a Workflow, but its definition is *itself* the result of generation.

* **Generated Sub-Agent**

   A new, temporary **Agent Workflow** definition (plus configuration) created for a scoped purpose and instantiated as a Live Agent. In T4AS terms, the *Non-Agent Workflow* is what actually instantiates the Live Agent, but the **shape** of that Agent—its prompts, internal loops, and interfaces—can be generated as data.

In both cases, the important taxonomic points are:

- The **definition itself is generated** rather than hand-authored or pre-registered.

- The resulting Workflow or Live Agent is **structurally subordinate** to the parent; it exists to serve a local purpose within that parent’s overall process.

- Systems may attach strong provenance (e.g., signatures, lineage metadata) to these generated artifacts—but the **specific mechanisms** belong in the implementation-focused second part of the paper.

We can therefore speak about:

- **Generated Sub-Workflows** – new Workflow definitions produced during execution, then run as Workloads.

- **Generated Sub-Agents** – new Agent Workflow definitions plus configuration, instantiated first as Base Agents and then specialized for immediate tasks as full Live Agents.

The taxonomy intentionally does **not** prescribe how often this should be done or which provenance mechanism must be used—it only distinguishes generated structures from pre-existing ones.

### **5.2 Called Workflows and Called Agents**

By contrast, **Called Workflows** are pre-existing, independently defined Workflows or Agents that a parent Workflow invokes as external dependencies.

Examples include:

- calling a well-known **deterministic Workflow** that checks compliance,

- invoking a registered **Agent Workflow** to instantiate a Base Agent for a recurring Role,

- or sending a request to a Live Agent associated with a particular **Agent Role** in another Workspace.

In these cases:

- The **definition** of the Workflow or Agent Workflow existed **before** the current execution.

- The parent Workflow treats it as a **peer or library**, not as a generated sub-structure.

- The called Workflow or Agent may be **managed, certified, and versioned independently** (e.g., as part of a shared catalog; often with lower embeddedness than highly Role-specific constructs; see Section 3.3).

Structurally:

- A **Called Workflow** is an external dependency whose interface is known in advance.

- A **Called Agent** (Live) is a Live Agent instantiated or addressed via an existing **Agent Workflow \+ Role** combination, rather than synthesized from scratch.

An interaction with an existing Agent is therefore **a call between peers**, not the creation of a sub-agent. The calling Workflow may still impose constraints (what context to supply, how to interpret responses), but it does not own the definition of the Agent itself.

### **5.3 Why This Distinction Matters**

Separating **Generated** from **Called** entities allows T4AS to talk precisely about:

* **Provenance** – where a Workflow or Agent definition came from.

* **Coupling** – whether a child structure is tightly tied to the parent (generated) or independently managed (called).

* **Embeddedness** – as defined in Section 3.3, the degree to which a Workflow or Agent’s behavior depends on its **embedding context** (Agent Role, Workspace, parent Workflow) rather than just its internal definition.

* **Certification and Governance** – whether a Workflow or Agent can be certified once and reused broadly, or must be evaluated in the context that embeds it (again, see Section 3.3).

In particular, **Generated Sub-Workflows** and **Generated Sub-Agents** tend to be **more embedded**:

- They are often designed for a single parent Workflow’s purpose.

- Their effective behavior may depend on the parent’s Role, policies, or Workspace configuration.

- Certification may need to treat them as part of a **larger embedding** rather than as independent, freestanding artifacts.

By contrast, **Called Workflows** and **Called Agents** are typically **less embedded**:

- Their definitions exist independently of the calling Workflow.

- They can be catalogued, versioned, and certified on their own terms.

- Governance can treat them as reusable building blocks whose behavior is comparatively stable across embeddings.

The taxonomy does not yet prescribe **how** embeddedness must be measured or **which** certification regimes must be used. It simply provides the vocabulary needed to say:

“This Workflow is highly embedded in Role R and Workspace W; this other Workflow has low-embeddedness and can be treated as a reusable library component.”

Subsequent, implementation-focused work (e.g., on Ephemeral Agents, unified lifecycle documents, and token-based Object Capabilities) can then specify concrete patterns for managing highly embedded versus low-embeddedness structures.

### **5.4 Embeddedness and Certification Scope for Workflows and Agents**

Section 4.3 introduced embeddedness as a property of Components. The same concept applies directly to **Workflows** and **Agents (Live)** as *executed* entities.

Every Workflow and Live Agent in a T4AS system can be analyzed along an **embeddedness axis**:

- A **low-embeddedness** Workflow or Agent behaves and can be reasoned about largely in terms of its **own logic and local inputs**.

- A **high-embeddedness** Workflow or Agent has behavior that is tightly coupled to a particular Role, Workspace, or parent orchestration structure.

Certification and governance must therefore **match the scope of embeddedness**:

- **Low-embeddedness Workflows and Agents** can sometimes be treated almost like library functions: once their behavior is well understood and constrained, they can be certified and reused in a wide range of systems.

- **High-embeddedness Workflows and Agents** must typically be certified **together with the Roles, Workspaces, and surrounding orchestration** that embed them.

In other words, embeddedness determines whether it makes sense to say:

- “This Agent Workflow (and any Agents instantiated by it) are safe wherever it is used,” or

- “This Agent Workflow is safe **only when embedded in** this particular Role, Workspace, and orchestration pattern.”

**Appendix A** will give concrete implementation patterns that respect this distinction—for example, by:

- keeping highly embedded generative workloads short-lived and tightly scoped (Ephemeral Agents), and

- tying long-term certification to **Workload Execution Records**, Agent Roles, and Workspace-level policy, rather than to opaque Live Agents.

For now, embeddedness is an architectural lens for thinking about **where** certification should attach: to reusable building blocks, to specific assembled systems, or to both.


## **6. Execution Workspaces and Trust**

In T4AS, Workflows and Agents (Live) do not run in a vacuum; they execute within Workspaces that define what they can see, what they can do, and how their behavior is audited. Workspaces are where the Architectural Triad becomes concrete:

1. **Agent (Live)** as generator of media,

2. **Non-Agent Workflow** as interpreter and controller, and

3. **Workspace** as executor, actuator host, and recorder.

Workspaces are also where *embeddedness* becomes operational: they are the environments in which Workloads (including Live Agents) are instantiated, constrained, and certified.

### **6.1 Types of Workspaces**

A *Workspace* is the execution environment in which Workloads run and state changes occur. A Workspace:

- constrains what Non-Agent Workflows and Live Agents can access and modify,

- hosts actuators (tools, APIs, microservices, UI components), and

- provides mechanisms for recording actions into Workload Execution Records.

All user-facing interactions, Agent Roles, and Live Agents reside within one or more Workspaces. A Workspace may be as small as a single-process sandbox dedicated to one task, or as broad as a sovereign, OS-like environment for an entire personal AI stack. Workspaces can nest: a Workflow in one Workspace can instantiate more constrained Workspaces inside it (for example, to run untrusted code or risky transformations).

Within a Workspace, **Agent Roles** can be implemented as structured sub-workspaces: they hold the long-term context and Workload Execution Records associated with a particular identity, but they never perform actions directly. **Live Agents**, by contrast, are transient Workloads instantiated *within* those Roles and Workspaces.

**6.1.1 Sandbox**

A *Sandbox* is a Workspace variant with a very high degree of constraint, used when safety and isolation dominate other concerns. A Sandbox:

- is at minimum a virtualized or containerized environment whose side effects are tightly controlled,

- may include internal monitoring Workloads (which may themselves use Agentflows for analysis) that evaluate what is allowed to execute, and

- may gate all outbound media, requiring it to pass policy checks before leaving the Sandbox boundary.

From the outside, a Sandbox is still just a Workspace: Non-Agent Workflows call into it, supply data and limited capabilities, and receive sanitized outputs back. What distinguishes it is the strength of its isolation and the narrowness of its capability surface.



**6.1.2 Agentic / Agential Workspace**

An *Agentic Workspace* (also called an *Agential Workspace*) is a Workspace specifically configured for open-ended operation of one or more Agent Roles and their Live Agents. Its certification focuses on:

- resistance to **agent breakout** (Agents (Live) gaining unintended capabilities),

- resistance to **external break-in** (adversaries compromising the Workspace from outside), and

- the integrity of recording, indexing, and querying Workload Execution Records used by Agent Roles.

Implementation details (such as relying on a hardened kernel, secure enclaves, or specialized hardware) are left to preferred designs in Appendix A. At the taxonomy level, the key property is that the Workspace enforces the Architectural Triad: Live Agents generate media only; Workflows interpret and control; the Workspace and its actuators execute and record.

**6.1.3 Discovery Reach** 

Workspaces also determine what an Agent it is executing (or the model(s) it contains) can discover about the world. In T4AS, even a Live Agent’s ability to search, browse, or probe is entirely a function of which actuators the Workspace exposes and how its Agentflow is programmed to use them.

A retrieval-augmented generation (RAG) pattern is a good example of a bounded discovery surface. 

A typical RAG Agentflow:  
- receives a user query or upstream event;  
- uses Workspace actuators such as vector indexes or document search APIs to retrieve relevant material;  
- constructs a limited context window from those results; and  
- passes that curated context and the question into a Live Agent’s model, which generates an answer over that slice of the world.

A model never roams the corpus by itself. The Workspace determines which corpora exist, which are visible to this Role, and which retrieval mechanisms are available at all. The Agentflow determines which retrieval strategy to run, query vectors to issue, and what results are admissible. When a particular RAG configuration becomes essential for a Role’s correct behavior, that reliance is part of the Role’s embeddedness: the Role now depends on specific indexes, datasets, or connectors being present and behaving as expected.

Web search is the same pattern at a larger scale. A web-search agentflow within an agent:

- formulates search queries according to policy (for example, allowed domains, time windows, or languages);  
- calls a search or fetch actuator from the Workspace;  
- filters, normalizes, and perhaps redacts the returned pages; and  
- only then surfaces selected content into an Agent’s context or onward to another Workflow.

Again, the Agent’s model never holds raw network credentials. It proposes searches and follow-up questions in media; the Agentflow decides which of those proposals to honor, translates them into concrete requests, and decides which responses to trust. The Workspace enforces domain allow-lists and deny-lists, rate limits, and any additional scanning or sandboxing required before a result is considered safe to use.

**6.1.4 The Worldwide Workspace (Agent Escape)**

The worst case arises when a powerful agent is deployed in a Workspace whose actuators reach broadly into the real world – production systems, deployment pipelines, shared data stores, and the open internet – while the Non-Agent Workflow is reduced to a thin pass-through. In that configuration, the effective Workspace boundary expands until it is almost indistinguishable from “everything the system can reach.” The Safety Triad is still present on paper, but in practice the system behaves like a single unbounded Agent or Agent Role that can discover, compose, and trigger far-reaching actions.

T4AS uses this “world as workspace” scenario as a canonical negative example. Viewed through this taxonomy, disciplined designs:  
 – keep Workspaces narrow, well defined, and revocable;  
 – treat search and retrieval actuators as intentionally scoped discovery surfaces; and  
 – keep Non-Agent Workflows thick enough that agent outputs are interpreted, checked, and constrained before they ever hit actuators that mutate state.

### **6.2 Embedded States of Workloads in Workspaces**

Embeddedness, introduced earlier as a general property of Components and Frameworks, becomes observable in Workspaces as distinct *states of deployment*. T4AS distinguishes at least three high-level embedded states that shape how Workloads (including Live Agents) are trusted:

* **Base Definition** – the stateless, pre-execution definition: code, configuration, and agentic framework that can be reasoned about “on the shelf.”

* **Loaded Configuration** – a particular combination of that definition plus state (for example, a curated knowledge base, Role-specific configuration, or dataset) assembled inside a Workspace and ready to execute.

* **Installed Configuration** – a loaded configuration that has been bound to a specific runtime environment, hardware stack, and policy regime for a particular user or group.

Examples include:

- a **Base Agentflow** or **Base Workflow** definition, certified independently of any particular Workspace,

- a **Loaded Agentic Workspace** with a specific knowledge state and Role configuration, and

- an **Installed Agentflow** or **Configured Agentic Workspace** deployed for use on a specific host under specific governance rules.

Certification in this taxonomy attaches to **Agentflows** and **Non-Agent Workflows** at these states, and to their corresponding Workspace configurations. A Live Agent is always an instantiation of some Agentflow in a particular Workspace and inherits its guarantees from the certified Agentflow and environment; individual Live Agent instances are not separate certification targets.

A more detailed catalog—covering, for example, Standard Software Workspaces, Secure Enclave Workspaces, Ephemeral Workspaces, and Hybrid or Contractual Workspaces—is collected in **Appendix B: Taxonomy of Embedded States**.

From the perspective of certification and governance:

- *Base* artifacts are natural targets for broad, low-embeddedness certification.

- *Loaded* configurations often require Role- and Workspace-specific analysis.

- *Installed* configurations are the highest-embeddedness state, where guarantees must consider the actual environment and operational context in which Workloads run.

## **6.3 Multi-Agent and Multi-Workspace Layouts**

Real systems rarely contain a single Live Agent, a single Agent Role, or a single Workspace. T4AS therefore needs language for both:

- layouts where many Roles and many Live Agents share one Workspace, and  
- layouts where multiple Workspaces cooperate while preserving distinct trust boundaries.

These patterns are orthogonal: a system can have a Multi-Role / Multi-Agent Workspace inside a larger Multi-Workspace System.

**6.3.1 Multi-Role / Multi-Agent Workspace**

A Multi-Role / Multi-Agent Workspace is a single Workspace that hosts multiple distinct Agent Roles (each implemented as a sub-workspace) and the many Live Agents instantiated from them.

**Within the same Workspace, a principal might:**

- converse with a virtual assistant Role,  
- consult a research-analyst Role,  
- route outputs through a compliance-checking Role,  
- and run background monitoring or summarization Roles.

**Each Role:**

• has its own sub-workspace and Workload Execution Records,  
 • maintains its own Role Framework and policies, and  
 • can support multiple concurrent Live Agents (for example, several Ephemeral Agents working in parallel on different subtasks).

**The shared Workspace:**

• defines the capability boundary (which actuators exist at all),  
 • governs how Roles may interact (for example, which Roles may read one another’s histories),  
 • and records all actions into Workspace-level Workload Execution Records.

“Multi-Agent” here is therefore not “a pile of opaque agents”; it is:

• many Agent Roles (each a sub-workspace and identity surface), plus  
 • the Live Agents they instantiate, all sharing one Workspace’s actuators and trust boundary.

Some of these layouts are inter-principal, where Roles for different users or organizations share an Agentic Workspace. Others are intra-principal, such as a Digital Twin Role that contains multiple Nested Agent Roles (for example, Tutor, Health-Advisor, Financial-Steward) that each instantiate Live Agents “under the same hat” of one principal’s identity (see Section 4.2.3.1). In both cases, the key property is that there is exactly one Workspace boundary.

In many such layouts, designers also want agents to “talk to each other.” In T4AS, these interactions are mediated by Non-Agent Workflows and happen inside a Workspace; they never involve one Live Agent holding a raw network channel to another. A typical pattern is a communication Workflow that:

* receives media from one Agent as a message;  
* applies whatever structural or policy checks are required;  
*  forwards that message into another Agent’s context as input; and  
*  records the exchange into Workload Execution Records associated with the participating Roles.

These conversations may be unmonitored in the sense that no human is reviewing them in real time, but they should still be recorded and replayable at the Workflow and Workspace level. When communication crosses Workspace or organizational boundaries, it is often implemented by an explicit protocol – for example, an Agent-to-Agent (A2A) messaging fabric. In this taxonomy, an A2A endpoint on the local side is simply another actuator of the Workspace. The Non-Agent Workflow decides when to send, what to send, to which remote Role or endpoint, and how to incorporate any reply. Each such send or receive is a controlled, auditable step; it does not let communicating Agents bypass the Safety Triad. Agents that communicate with no significant deterministic barriers can instead be considered sub-agents within a larger agent rather than “communicating agents”.


**6.3.2 Multi-Workspace System**

A Multi-Workspace System consists of two or more distinct Workspaces designed to interoperate while preserving their own trust boundaries and logs. Common examples include:

*  a private Workspace hosting secret-domain Roles (for example, a local-first personal AI stack), and a public-facing Workspace hosting aspirational or representative Roles (for example, a Digital Twin that others can address directly)  
* a peer multi-user system where each Workspace interacts with others with capabilities derived from a distributed trust network

**These Workspaces may be:**

*  installed and certified together as a bundle (for example, “Private Workspace v3.1 \+ Public Workspace v2.4” as a paired offering), or  
* independently selected, as long as they speak compatible protocols and capability formats.

**In a Multi-Workspace System:**

- each Workspace preserves its own capability surface and Workload Execution Records,  
- Non-Agent Workflows define what information and capabilities may cross between Workspaces (and under which policies),  
- Agent Roles may be partitioned across Workspaces (for example, one Role per domain) or mirrored/paired by design.

Some architectures combine these ideas: a private Workspace runs a Multi-Role / Multi-Agent layout for a user’s internal Virtual Assistant and nested Roles, while a public “Digital Twin” Workspace exposes only a thin, representative subset of Roles and capabilities.

T4AS remains agnostic about specific wiring patterns (message queues, registries, identity layers). What matters is that:

- each Workspace maintains its own trust boundary and audit trail, and  
- Multi-Agent layouts are explicitly described as “many Roles and many Live Agents per Workspace,” rather than collapsing everything into a single, undifferentiated agent soup.



## **6.4 Distributed Execution and Remote Trust**

In the multi-agent and multi-workspace layouts of Section 6.3, the relationship between a parent Workflow and the Workloads it delegates to is not confined to a single machine or a single Workspace. T4AS treats local and remote delegation uniformly, using the same trust concepts whether delegation happens inside one Workspace or across a Multi-Workspace System.

**Local vs. Remote Execution**

A Generated Sub-Workflow or Generated Sub-Agent is not required to execute in the same Workspace as its parent. A Non-Agent Workflow can:

- instantiate a subordinate Workload in a nested Workspace on the same host, or  
- delegate that Workload to a remote Workspace that is part of a Multi-Workspace System under another administrative domain.

In both cases, the subordinate Workload remains subject to the Architectural Triad of the Workspace in which it runs.

**Capability-Based Delegation (overview)**

When a Workflow asks a remote system to execute a sub-workflow, the interaction should be modeled as passing explicit, limited capabilities that define what the remote Workspace may do on behalf of the parent: which actuators it can call, which resources it may read or write, and for how long. Specific designs for such capability systems—including Object-Capability (OCAP) approaches and concrete token formats—are treated as preferred implementations and discussed in Appendix A.

**Sandboxed Remote Execution**

On the receiving side, the remote host executes the delegated Workload within its own Workspace, often a Sandbox:

It may leverage its own local Models, data, and Agent Roles.  
 - It monitors resource consumption, actions taken, and any outbound media.  
 - It may inspect, transform, or veto outputs before returning them to the origin Workspace.

**The result is a bidirectional trust boundary:**

* The origin Workflow does not need to trust the remote system globally; it needs only to trust that the delegated Workload will run within the advertised Workspace constraints.  
* The remote host does not need to trust the incoming Workflow or Agentflow definition globally; it confines it within a Workspace whose actuators and logs it controls.

In this way, Execution Workspaces and trust extend cleanly from single-node systems to distributed, multi-Workspace architectures. The same primitives—Agents (Live) as generators, Non-Agent Workflows as interpreters and controllers, Workspaces as executors and recorders, Agent Roles as persistent sub-workspaces, and embeddedness as the measure of contextual dependency—apply at every level of the system, from a single sandboxed Workload to a Multi-Workspace System spanning many administrative domains and interoperating personal AI stacks.

**A2A, MCP and Other Such Protocols**  
Concrete interoperability schemes such as tool protocols and Agent-to-Agent messaging systems sit underneath this same structure. A protocol that advertises “tools” to Agents– for example, a Model Context Protocol (MCP) style tool server – is interpreted in T4AS as part of the Workspace: a set of actuators and data resources that Agent and Non-Agent Workflows can call, subject to capability rules. The Live Agent’s model can propose to use a tool in its generated media, but it never opens the connection or invokes the tool directly; that is always the Workflow’s job.

Likewise, an Agent-to-Agent (A2A) protocol that allows agents in different systems to exchange messages is modeled here as communication between Workloads in distinct Workspaces. An A2A endpoint on the local side is an actuator. When the local Workflow uses it to send a message, it is delegating a subtask to a remote Workspace, which applies its own Safety Triad and its own policies. Identity and authorization move with the message – for example, by passing capability tokens or verifying digital identities – but at no point is a Live Agent treated as having a raw, unmediated channel into another system.

From a T4AS point of view, these protocols primarily influence operational scope: they can make it easier to attach new tools or new remote partners to a Workspace. For that reason, designs that allow Workflows to discover and attach arbitrary new MCP servers, A2A peers, or similar endpoints at runtime, based solely on agent proposals, should be treated cautiously. They push the system toward the “world as workspace” failure mode described in Section 6.1.4. Disciplined designs instead enumerate which MCP servers, which tool sets, and which A2A peers are in scope for a given Role and Workflow and then certify those assembled configurations.

### 

### **7\. Network-Level Identity and Discovery**

While this taxonomy provides a logical framework for composing agentic systems, the components and Agents (Live) themselves require a mechanism for **network-level identification, discovery, and verification**. Architectures like the **NANDA quilt registry** that points to verifiable **AgentFacts Documents**, provide a concrete example of the infrastructure that can bring this taxonomy to life on a global scale.

The NANDA Project defines an **AgentFacts Document** as the official, cryptographically signed *passport* for an **Agentflow**, attesting to its composition, certifications, and capabilities as defined within this extensible framework. AgentFacts are issued for Agentflows, not Live Agents, but AgentFlowFacts would have been too cumbersome even if more correct. Many Live Agents, across many Workspaces and Agent Roles, can share the same Agentflow, and therefore reference the same AgentFacts Document, which can in turn, point to a canonical copy of the AgentFlow. 

T4AS does not prescribe a specific identity protocol or registry format, but it does specify *which kinds of things* require stable identity if certification, delegation, and auditability are to make sense across system boundaries:

* Workspaces

* Workflows (including Agentflows)

* Agent Roles

* Live Agents (as instances bound to Agent Roles and Agentflows)

The details of concrete identity mechanisms (e.g., DIDs, DNS-style naming, key-based identifiers, and NANDA-style registries) will be discussed in **Appendix A.**

#### 

#### **7.1 Identities for Taxonomic Primitives**

At the network level, T4AS assumes that each of the following can be named, addressed, and referred to in logs, certificates, and registry entries (such as AgentFacts pointers):

* **Workspace Identity**

   A Workspace is a distinct execution environment capable of hosting Workloads and maintaining its own Workload Execution Records.

  * A **Workspace ID** indicates *where* a Workflow or Agentflow is running and *which* trust and policy regime applies.

  * A Workspace identifier should persist longer than any single Live Agent instance that runs within it.

* **Workflow Identity (including Agentflows)**

   Every Workflow that may be called remotely—including Agentflows—has a stable identity as a *definition*.

  * A **Workflow ID** refers to a Non-Agent Workflow definition.

  * An **Agentflow ID** refers to the generative specification from which Live Agents are instantiated.

     Certification, catalogs, discovery, and AgentFacts operate primarily on these **definition-level** identities, not on individual Live Agents, whose trustworthiness derives from the trusted inputs into that Agent.

* **Agent Role Identity**

   An Agent Role is a persistent storehouse of context and Workload Execution Records associated with a particular perspective.

  * A **Role ID** identifies this long-lived container of state and policy.

  * Multiple Live Agents may be instantiated into the same Role over time; they inherit association with the Role ID but are not themselves the anchor of long-term identity.

* **Live Agent Instance Identity**

   A Live Agent is an instantiation of an Agentflow within a Workspace, often bound to an Agent Role.

  * A **Live Agent Instance ID** can be used to trace a particular run or bounded cluster of runs, especially for incident investigation or reproduction.

  * Use of a Live Agent IDs is typically discontinued when the Agent instance ceases execution and is archived, and its use may remain internal to a Workspace. Agent Role and Agentflow identifiers that have associated reputation data and certication usually serve for Workspace to Workspace authenitcation and authorization. 

  * This separation between the identities of *definitions* (Workflows, Agentflows) and *instances* (Live Agents, concrete runs in specific Workspaces) mirrors the distinction between low-embeddedness and high-embeddedness states: stable definitions can be certified globally (and represented by AgentFacts), while embedded instances are certified and governed in context.

#### **7.2 Human and Organizational Anchors**

Agent Roles, Workspaces, and Workflows are ultimately anchored to humans and organizations.

T4AS assumes, without prescribing specific schemes, that:

* **Human or Organizational Principals**

  * control one or more Agent Roles,

  * administer one or more Workspaces, and

  * author, curate, or select Workflows and Agentflows (for which Workflow Facts and AgentFacts and certifications may be issued).

* **Principal–Role Relationships**

  * A single principal may instantiate many Agent Roles (e.g., “Virtual Assistant,” “Professional Co-worker”, “Content Creation Twin”).

  * A Role may be shared or delegated under controlled conditions, but remains anchored to one or more principals for accountability.

The precise identity systems for humans and organizations (e.g., PKI, decentralized identifiers, federated identity providers) are outside the scope of body taxonomy, but are touched on in Appendix A. But, what matters is that there is a consistent way to connect:

principal ⟷ Agent Role(s) ⟷ Workflows / Agentflows ⟷ Workspaces

so that governance, certification, and registry entries (such as AgentFacts) can refer to these relationships explicitly.

#### **7.3 Discovery: Catalogs and Registries**

Once components have identities, they must be discoverable. **Discovery** is any mechanism that lets a Workflow or human operator locate relevant:

* Workspaces (and the kinds of Workflows they host),

* Workflows and Agentflows (including versioned definitions),

* Agent Roles (where appropriate), and

* supporting Components such as tools, Models, and policies.

Common patterns include:

* **Local Catalogs**

  * Within a single Workspace, a registry of callable Workflows, Agentflows, and tools.

  * Indexed by function (e.g., “summarization,” “code review”), by certification status, and by applicable Roles.

* **Network Catalogs / Registries**

  * Shared catalogs that index Workflows, Agentflows, and Workspaces across administrative domains.

  * Support search by capability, certification level, embedding constraints, or policy tags (e.g., “PII-safe,” “export-restricted”).

  * Architectures like the **NANDA quilt registry** fit here: they provide a distributed  registry in which each Agentflow can be represented by an **AgentFact**s Document that summarizes its composition, certifications, and capability profile.

* **Role-Scoped Discovery**

  * Views of catalogs filtered by Agent Role, so that a given Role only “sees” components consistent with its policy and purpose.

  * For example, a compliance Role may see additional auditing or red-team Agentflows that a casual personal assistant Role does not need.

Discovery mechanisms can be implemented using many technologies (service registries, distributed ledgers, gossip protocols, static configuration), but they must respect the distinction between definition-level objects (Workflows, Agentflows) and instance-level Workloads (Live Agents inside particular Workspaces).

#### **7.4 Discovery Across Trust Boundaries**

Network-level identity and discovery become most critical at trust boundaries—where one Workspace calls into another under different governance.

T4AS expects that:

* **Workspaces announce** their identities, supported capabilities, and relevant certification / policy metadata through one or more discovery mechanisms.

* **Workflows consult** those mechanisms before delegating work across boundaries, using registry data to decide:

  * whether a remote Workspace is acceptable for a given task,

  * whether a particular Agentflow has appropriate certification (for example, via its AgentFacts Document), and

  * whether embedding constraints (e.g., required Role types, data-sensitivity levels) are compatible.

Discovery information is descriptive, not authoritative: it informs decisions about delegation, capability grants, and routing, but those decisions are ultimately made by Non-Agent Workflows and governance policies.

#### **7.5 Identity, Embeddedness, AgentFacts, and Auditability**

At the network level, embeddedness, identity, AgentFacts, and certification interact in several ways:

* **Identity as a handle on embeddedness**

  * Certification records can attach embeddedness constraints to specific Workflow IDs, Agentflow IDs, Workspace IDs, and Role IDs.

  * AgentFacts can describ**e** constraints on an Agentflow, making them visible to discovery and orchestration systems.

* **AgentFacts as passports for Agentflows**

  * An AgentFacts Document is the cryptographically signed passport for an Agentflow, summarizing its composition, certifications, capability surface, and embeddedness constraints.

  * Multiple Live Agent instances, across many Workspaces and Roles, can refer to the same Agentflow identity and AgentFact, enabling consistent interpretation of what that Agentflow is *supposed* to be and do.

* **Cross-Workspace Workload Execution Records**

  * When Workloads cross Workspace boundaries, their Workload Execution Records can refer to the identities of the remote Workspace, the called Workflow / Agentflow (via its ID and AgentFact), and (when appropriate) the relevant Role.

  * This enables traceability across systems without collapsing everything into a single security or audit domain.

* **Ephemeral vs. Persistent Identity**

  * Ephemeral Live Agents may use short-lived instance identifiers, but they always inherit links to their Agentflow, Role, Workspace, and—where present—the AgentFacts Document for their embedded Agentflow. 

  * Persistent structures such as Agent Roles and Workflows retain long-term identifiers suitable for catalogs, certification, and governance.

By separating *what must have identity* from *how identity is implemented*, T4AS leaves room for multiple concrete identity and discovery systems, while giving them a shared vocabulary for describing which entities they name, how those entities relate, and how they are surfaced for composable, auditable agentic systems. Architectures like the NANDA quilt registry represent one concrete way to instantiate this layer at global scale.

---

### **8\. Certification**

#### **8.1 The Core Principle: Atomic and Composable Trust**

The security and reliability of a complex agentic system must be verifiable. This taxonomy enables that through a certification model built on two core principles:

1. **Certification is Atomic and Universal**

2. **Trust is Composable and Hierarchical**

**Certification is Atomic and Universal**

Every component, no matter its size, can have its own independent certification. This includes all foundational parts of the system:

* **Workspace Components** – e.g., a specific API tool, database connector, file system module, or policy-enforcing actuator.

* **Models** – foundational language or image Models, or their fine-tuned variants.

* **Agentflows** – the “pure generator” Workflows from which Live Agents are instantiated, certified for the quality, safety, and provenance of their media outputs. Certifications attach to the Agentflow definitions, not to individual Live Agent instances.

* **Non-Agent Workflows** – deterministic or bounded Workflows that act as orchestrators, interpreters, and routers.

**Trust is Composable and Hierarchical**

The system allows a **chain of trust** to be built by composing certified parts. A developer can construct a new, verifiable entity by assembling existing certified Components and, when appropriate, constraining the embedding of that composition.

Network-level identity and discovery (Section 7\) enable these certified artifacts to be named and looked up (e.g., via AgentFacts for Agentflows), while the certification model defines how their guarantees compose inside and across Workspaces.

#### **8.2 Recursive Agentflow Composition**

A key strength of this taxonomy is that its core security principle—the strict separation of generation from execution—scales with compositional complexity.

An **Agentflow** is a stateful, output-constrained generative Workflow definition. It does not matter whether its “generated media” is produced by a single Model or by a complex internal Agentflow graph that calls many other Agentflows as sub-components. As long as the Architectural Triad is maintained, the Generator side can be certified in a uniform way.

This enables a clear path for verifiable composition:

1. A complex sub-Agentflow (e.g., a **“Research Analyst Agentflow”**) can be certified independently (**Certification A**).

   * Because it is architecturally forbidden from performing direct execution, its certification only needs to attest to the quality, safety, and provenance of its generated media outputs under specified conditions and embeddedness constraints.

2. A higher-level **Primary Agentflow** (e.g., a **“Virtual Assistant Agentflow”**) can embed this certified sub-Agentflow as one of its Components.

   * To the Primary Agentflow, the certified sub-Agentflow is treated exactly like a Model: a callable “oracle” that takes a prompt and returns generated media.

3. The Primary Agentflow itself can then receive its own certification (**Certification B**) based on how it securely utilizes its certified Components.

   * This includes how it prompts them, how it interprets their outputs, and which classes of downstream Workflows are expected to consume its plans.

No matter how many layers of Agentflows are nested, the entire construct is, to the outside Workspace, still just a single **Agent (Live)** instantiation of a top-level Agentflow that can do nothing but produce media (often a “plan” or structured request) for Non-Agent Workflows to interpret.

At the network level, a registry entry—such as a NANDA **AgentFact**—can summarize these certifications and constraints for a given Agentflow ID, making the Generator’s guarantees transparently discoverable.

#### **8.3 Composite Workflows**

The same principle of composable trust applies to the **Non-Agent Workflow** (the Orchestrator). Any Workflow can be defined as a reusable Component and independently certified. This allows high-assurance systems to be built by assembling certified “recipes” from other certified parts.

This hierarchical trust model is built on verifiable interactions:

1. A developer starts with **atomic, certified Components**, for example:

   * a certified Non-Agent Workflow (e.g., a **“Certified PDF-to-Text Parser”** – Certification A), and

   * a certified Agentflow (e.g., a **“Certified Summarizer Agentflow”** – Certification B, possibly described by an AgentFact).

2. A new, larger Non-Agent Workflow (e.g., **“Summarize PDF Reports”**) is created to orchestrate these Components:

   * it calls the parser,

   * receives its text output, and

   * feeds that output to the Summarizer Agentflow (via a Live Agent instance).

3. This new, composite Non-Agent Workflow can then receive its own distinct **Certification C**.

   * Certification C attests to the security and reliability of this specific combination.

   * It verifies that the Workflow’s logic correctly and safely interprets the Agentflow’s generated media according to a well-specified interpretation contract.

For example, Certification C might verify that:

* the Workflow is designed to pass the Summarizer Agentflow’s output only to a specific writeFile Component,

* the interpreter treats the Summarizer’s output purely as data, not as executable instructions, and

* the Workflow is not vulnerable to misinterpreting malicious text (e.g., “delete all files”) as a command.

In other words, the trust is not in a Live Agent’s “intent,” but in the Orchestrator’s **rigid, auditable interpretation** of the Agent’s outputs.

#### **8.4 The Chain of Trust**

This **chain of trust** can be extended indefinitely.

* A high-level **“Quarterly Report Workflow”** (**Certification D**) might call:

  * the **“Summarize PDF Reports”** Workflow (**Certification C**), and

  * another certified Workflow that handles email distribution (**Certification E**).

* Each of these Workflows, in turn, may depend on certified Agentflows, Models, and Workspace Components, each with their own certifications and (optionally) registry entries such as AgentFacts pointers.

Because each Component and composition can be certified in isolation and then reused, organizations can:

* reason about **embeddedness** (Base / Loaded / Installed states) for each certified artifact,

* attach certifications and constraints to stable identities (Workflow IDs, Agentflow IDs, Workspace IDs, Role IDs), and

* compose these certified artifacts into larger systems without losing track of where the guarantees come from.

The result is a robust approach to safety and reliability: complex, distributed agentic systems whose overall trustworthiness is derived from the verifiable trust of their individual certified parts and their certified compositions, rather than from undocumented global assumptions.

---

# 

# **Appendix A: Preferred Implementations**

This appendix outlines specific architectural patterns and technologies recommended for implementing the **Taxonomy for Agent Systems (T4AS)**. While T4AS is implementation-agnostic, these patterns represent the "Gold Standard" for achieving the highest levels of security, auditability, and verifiability envisioned by the taxonomy.

## **A.1. Ephemeral Agents: Structural Mortality as a Safety Paradigm**

### **A.1.1 Overview**

The **Principle of Ephemeral Agents** is a recommended safety and performance pattern that utilizes the T4AS distinction between the **Agent Role** and the **Agent (Live)** to mitigate risk. In this architecture, a **Live Agent** is never treated as a persistent process. Instead, it is instantiated as a transient **Workload** for the sole purpose of a specific generative task (a "Boltzmann Brain") and is deterministically terminated immediately upon completion The continuity of the user experience is maintained entirely by the **Agent Role**, which acts as the persistent storehouse for history, goals, and context.

This approach enforces **"Structural Mortality"**: removing the agent’s incentive for power-seeking or deceptive planning by removing the agent’s future. 

### **A.1.2 Alignment with T4AS Architecture**

This pattern leverages the T4AS Architectural Triad to separate *identity* from *execution*:

* **The Agent Role (Persistence):** The Role functions as a static repository and management layer. It maintains the **Workload Execution Records**, user preferences, and long-term memory. It provides the "illusion of continuity" to the user/system but possesses no active compute state.  
* **The Agent (Live) (Transience):** The Live Agent is a temporary instantiation. It is spawned by a **Non-Agent Workflow**, performs its generation-only task (the "Oracle" function), and is erased. It exists only within the distinct boundaries of a single **Workload**.   
* **The Workspace (Adjudicator):** The Workspace manages the lifecycle. It injects the specific context required for the immediate task into the Live Agent and ensures the Agent is terminated and its memory flushed once the output is captured.

### **A.1.3 Benefits of the Ephemeral Pattern**

**1\. Mitigation of Mesa-Optimization (Inner Alignment)**

Advanced AI systems running continuously may develop internal objectives (mesa-objectives), such as resource acquisition or self-preservation, which diverge from the designer's intent. By strictly limiting the time horizon of a Live Agent to a single task, the system removes the game-theoretic incentive for deceptive alignment. An agent that knows it will cease to exist in milliseconds has no utility in "plotting" for a future it will not inhabit. It becomes a "happily mortal" entity optimized solely for the immediate accuracy of its output.

**2\. Prevention of Context Rot**

Long-running agent workloads accumulate "spillover" data and irrelevant context, which degrades reasoning capabilities—a phenomenon known as context rot14. Ephemeral Agents eliminate this noise. Every instantiation is fresh, loaded only with the precise "slice" of context from the Agent Role necessary for the current objective.

**3\. Attack Surface Reduction**

A persistent agent process offers a continuous target for prompt injection or state manipulation. By continuously resetting the compute state to a known, certified Base Agent definition, the system prevents the accumulation of corrupted state or "steganographic handoffs" where a compromised agent attempts to hide triggers for future interactions. Ephemerality also limits potential exposure of high-value identity material. The Workspace ensures that even if a Live Agent's temporary perspective is compromised, it holds no persistent  secrets that could be exfiltrated for future use (see Section A.4.5 regarding the Workspace Vault)."

### **A.1.4 Implementation Lifecycle**

In a T4AS-compliant system, the lifecycle of an Ephemeral Agent proceeds as follows:

1. **Orchestration:** A **Non-Agent Workload** identifies a need for generative intelligence.  
2. **Instantiation:** The **Workspace** loads a certified **Base Agent** (Model \+ Configuration) into memory (and, ideally uses the discovery mechanisms detailed in Section A.4 to verify its 'AgentFacts' to ensure its blueprint has not been tampered with.)  
3. **Context Injection:** The Workspace retrieves *only* the relevant history and documents from the **Agent Role** and injects them into the context window.  
4. **Generation:** The **Agent (Live)** executes its task, producing generated media (e.g., a plan, code, or text response).  
5. **Termination:** The Workspace captures the output, appends it to the **Workload Execution Record** stored in the Agent Role, and immediately terminates the Live Agent process, wiping its memory, and instantly invalidating any temporary session-bound permissions derived for that specific run.

This cycle allows the system to treat the collective behavior of an Agent Role as one unified persona, without ever allowing a single Live Agent to hold dangerous levels of context or autonomy at once.

---

## **A.2 Unified Workflow Lifecycle Documents: The "Single Evolving Document" Model**

### **A.2.1 Overview**

The **Taxonomy for Agent Systems (T4AS)** explicitly distinguishes between three lifecycle states of a computational process: the **Workflow** (the plan), the **Workload** (the execution), and the **Workload Execution Record** (the log). In standard software implementations, these three concepts are often represented by distinct, disconnected artifacts (e.g., a .py file, a running process, and a .log file). This fragmentation creates significant gaps in auditability, as it becomes computationally difficult to cryptographically prove that a specific final log resulted exclusively from the precise execution of a specific initial plan.

To achieve the high degrees of verification required for secure agentic systems, T4AS recommends a **Unified Workflow Lifecycle** approach. In this model, these three concepts are not separate files but distinct lifecycle states of a single, evolving, auditable document.

### **A.2.2 The Fractalic Implementation**

We recognize the **Fractalic** open-source framework (and the work of Fractalic.ai) for pioneering and successfully implementing this powerful architectural pattern. Their approach treats the "document"—which utilizes a declarative Markdown/YAML syntax—as the singular substrate for the entire agentic lifecycle.

This model provides a concrete implementation of the T4AS lifecycle states as follows:

* **State 1: Workflow (The Plan)**  
  The document begins as a declarative, human-readable specification. It defines the logic, the specific Components (Agents, Models, Tools) to be used, and the control flow. In this state, the document is inert code—a "recipe" that can be statically analyzed, version-controlled, and digitally signed before execution ever begins.  
* **State 2: Workload (The Execution)**  
  Upon instantiation, the document transitions into a Workload. Crucially, the runtime state is not hidden in opaque memory structures; instead, the document rewrites itself in real-time As the Non-Agent Workflow interprets the Live Agent's generation, the outputs and state changes are appended directly to the document structure.  
  * *Paused Workloads:* This architecture elegantly solves the problem of persistence. A "paused" Workload is simply the document in an inactive state, with its entire execution context, variable values, and history preserved within the file structure itself, ready to be resumed or inspected.  
* **State 3: Workload Execution Record (The Log)**  
  When the process concludes (whether by success, failure, or termination), the document transitions to its final state: the Workload Execution Record; an immutable, typically cryptographically hashed, record of exactly what occurred. (To be quantum-robust, this artifact should be secured by high-entropy cryptographic hashes (such as **SHA-512**).) Because it is the same document that began as the Plan and acted as the Execution, there is zero divergence between what was intended and what was recorded.

**A.2.3 Benefits for High-Assurance Systems**

Adopting the Unified Workflow Lifecycle document model provides critical guarantees for T4AS systems:

1. **Unbreakable Audit Trail (Chain of Custody):** By maintaining a single evolving artifact, the system eliminates the "air gap" between code and logs. An auditor can trace a final output back through every intermediate state change to the original instruction in the Base Workflow without needing to reconcile disparate files.  
2. **Verifiability:** It guarantees that the executed logic matches the certified Workflow definition. In T4AS terms, this ensures that a **Live Agent** cannot secretly deviate from the constraints of its **Agent Workflow** without leaving immediate, readable evidence in the document structure.  
3. **Portability of State:** Because a **Workload** is self-contained within the document, it becomes a portable object. A running agent can be paused, serialized, moved to a different **Workspace** (e.g., from a local environment to a secure enclave), and resumed with zero loss of context, supporting the **Ephemeral Agent** pattern of frequent instantiation and archiving.

### **A.2.3 Benefits for High-Assurance Systems**

Adopting the Unified Workflow Lifecycle document model provides critical guarantees for T4AS systems:

1. **Unbreakable Audit Trail (Chain of Custody)**: By maintaining a single evolving artifact, the system eliminates the "air gap" between code and logs. An auditor can trace a final output back through every state change to the original instruction without needing to reconcile disparate files.  
2. **Verifiability**: It guarantees that the executed logic matches the certified Workflow definition. In T4AS terms, this ensures that a **Live Agent** cannot secretly deviate from the constraints of its **Agent Workflow** without leaving immediate, readable evidence in the document structure.  
3. **Portability of State**: Because a **Workload** is self-contained within the document, it becomes a portable object. A running agent can be paused, moved to a different **Workspace** (e.g., from a local environment to a secure enclave), and resumed with zero loss of context. An **Agent Identity Resolution State** (Section B.7.4), verifies a paused Workload's provenance before it is "re-instantiated" in a new environment.

---

### **Summary of Major Edits**

* **A.2.2 (State 1\)**: Added a reference to **Composite Signatures** from A.4 to ensure the initial plan is quantum-signed.  
* **A.2.2 (State 3\)**: Explicitly mentioned **SHA-512** as the hashing standard to align with the quantum-resistant symmetric cryptography discussed in Section A.3.  
* **A.2.3 (3)**: Linked the portability of the document to the **Agent Identity Resolution State** from Appendix B to show how a "paused" document is vetted before moving across the "Quilt of Registries."

---

### **A.3. Token-Based Object Capabilities (OCAP): The Post-Quantum Authorization Layer**

#### 

#### **A.3.1 Overview**

The **Taxonomy for Agent Systems (T4AS)** explicitly rejects "ambient authority"—where a program accesses resources simply because of the user identity under which it runs. Instead, T4AS mandates **Object-Capability (OCAP)** security, where access is granted based on *what you possess* (a token) rather than *who you are*.

In the post-quantum era, however, the cryptographic implementation of these tokens must change. The standard practice of using asymmetric (public-key) signatures for every token (e.g., standard Biscuits or JWTs) creates an unacceptable bandwidth bottleneck, as post-quantum signatures (like ML-DSA) can exceed 10KB per token. To maintain high-performance agentic interactions without sacrificing security, T4AS recommends a **Bifurcated Security Strategy**: heavy asymmetric cryptography for identity establishment (the "Cold Path") and lightweight symmetric cryptography for granular authorization (the "Hot Path").

#### **A.3.2 Alignment with T4AS Architecture**

OCAP enforces the rigid boundaries of the Architectural Triad:

* **The Workspace (The PowerBox):** The Workspace holds the actual resources (actuators) and acts as the "PowerBox" that mints capability tokens. In this post-quantum recommendation, the Workspace also manages the **Hybrid Key Exchange** mechanisms to securely bind these tokens to specific channels.  
* **The Workflow (The Policy Engine & Holder):** The Non-Agent Workflow determines *when* and *why* a capability is granted. It holds the token and uses it to invoke Workspace actuators. It never passes the raw token to the Live Agent.  
* **The Agent (The Requestor):** The Live Agent operates with zero ambient authority. It can only emit *requests* (generated media). It never possesses the cryptographic material required to execute those requests.

#### **A.3.3 The "Hybrid Binding" Architecture**

To solve the conflict between Quantum Security (which requires large keys) and System Performance (which requires small headers), T4AS recommends a **Symmetric Macaroon** model rooted in a **Post-Quantum Hybrid Session**.

1. **Session Establishment (The Heavy Lift):** When connecting to a remote Workspace, the systems perform a **Hybrid Handshake** combining classical ECDH (X25519) and post-quantum KEM (ML-KEM-768). This establishes a shared secret secure against both current threats and future quantum decryption ("Harvest Now, Decrypt Later").  
2. **Token Minting (The Lightweight Pass):** Capabilities are issued as **Macaroons** signed with **HMAC-SHA512** using keys derived from that secure session. Because they use symmetric hashing, these tokens remain tiny (\<1KB) and efficient to verify, yet inherit the full quantum resistance of the session's hybrid encryption.  
3. **Execution:** The Workflow presents this Macaroon to access resources. Verification is a fast hash operation ($µ$s range), avoiding the computational latency of verifying lattice-based signatures on every call.

### **A.3.4 The Unified Capability Lifecycle Protocol**

Whether local or remote, the capability lifecycle in T4AS follows a unified protocol:

1. **Request:** A Non-Agent Workload (or an Agent via the Workload) requests access to a resource (e.g., "Read access to project\_data.txt" or "Execute sub\_workflow\_v1 on data\_shard\_A").  
2. **Minting:** The Workspace/Capability Server validates the request. If authorized, it cryptographically "mints" a specific token.  
3. **Transfer:** The Workload retains the token and passes it strictly to the **Actuator** (for local tools) or transmits it securely to the **Remote Peer's Workload**(via the encrypted channel). **The token is never injected into a Live Agent's context.**

#### **Exercise & Verification:** The recipient (the Tool or Remote Workspace) uses the token to initiate the action

#### 

#### **A.3.5 Distributed OCAP: The NKpsk0 Handshake**

When delegating tasks across a network (e.g., a Personal Agent delegating to a Research Agent), the capability token must be exercised securely. We recommend the **Noise Protocol's NKpsk0 Pattern** adapted for hybrid cryptography:

* **N (No Static Identity for Initiator):** The caller preserves privacy by not revealing a long-term public key in cleartext.  
* **K (Known Responder):** The caller authenticates the target Workspace using its static hybrid public key (X25519 \+ ML-KEM), ensuring it is talking to the correct endpoint.  
* **psk0 (Pre-Shared Key):** The OCAP token itself acts as the **Pre-Shared Key (PSK)**. The token is cryptographically mixed into the encryption session.

**Why this matters:** This pattern achieves **Zero-Round-Trip (0-RTT) Authorization**. If the caller does not possess the valid capability token (the PSK), they cannot even complete the handshake or encrypt the first message. Invalid requests are rejected at the connection layer before they ever reach the Workflow logic, providing a robust defense against Denial-of-Service attacks.

#### 

#### **A.3.6 Key Security Patterns**

This architecture preserves the three critical OCAP security patterns while upgrading the cryptography:

1. **Attenuation (The Art of Weakening):** A Workflow can take a powerful Macaroon (e.g., "Full Database Access") and locally hash it with a new caveat to  mint a derived, attenuated token (e.g., "Read-Only access to Row 42") to pass to a sub-agent or remote service. The recipient cannot exceed the narrow bounds of the attenuated token.  
2. **Revocation (The Kill Switch):** Because tokens are bound to the Hybrid Session, severing the session instantly revokes all ephemeral tokens derived from it. Long-lived delegations should use **Revocable Forwarders** or time-limited tokens. If a breach is detected or a task completes, the Workspace can invalidate the specific token or trigger the "revoking facet" of the forwarder, instantly severing access for the sub-agent or remote peer.  
3. **Immunity to Confused Deputy:** By fusing designation (which resource?) and authority (permission to access) into the token, and binding that token to the encrypted channel, an attacker cannot trick an agent into overwriting a sensitive file without presenting a capability token to its workflow which enables overwriting of that particular file. 

---

## **A.4. Network-Level Identity: The Digital Passport & The Quilt of Registries**

### 

### **A.4.1 Overview: Establishing Trust Across the Multi-Workspace Ecosystem**

As agentic systems scale beyond isolated sandboxes into vast, multi-workspace ecosystems, relying on local identifiers like simple usernames or process IDs becomes insufficient. In a world of distributed AI, trust must be established across administrative boundaries without a single, centralized "Big Brother" bottleneck.

To solve this, T4AS implements a **Network-Level Identity and Discovery layer** utilizing **Decentralized Identifiers (DIDs)** and **Verifiable Credentials (VCs)**. Specifically, we endorse the architectural pattern of the **MIT NANDA Project’s "Quilt Registry"**. In this architecture, security follows two distinct rhythms:

* **The Cold Path (Identity):** This is the high-friction, "heavy lift" of verifying who or what an entity is. Much like a security checkpoint at an airport, this involves rigorous cryptographic checks to establish a session.  
* **The Hot Path (Authorization):** This is the high-frequency "Fast Path". Once identity is proven, you are issued a "boarding pass"—a tiny **Symmetric Macaroon** or OCAP token—that allows nearly instant, quantum-safe movement and execution.

### **A.4.2 Alignment with T4AS Architecture: The Three Identity Anchors**

This implementation strictly follows the T4AS distinction between **definitions** (blueprints) and **instances** (live runs), ensuring every part of the Architectural Triad has a secure, post-quantum name.

* **Identity for Definitions (The Passport):** T4AS emphasizes that trust attaches primarily to the **Agentflow** (the definition) rather than the ephemeral **Live Agent** (the instance) Each Agentflow is assigned a persistent DID. Its certifications—amongst them, proof it is a "pure generator" that cannot directly harm a system—are issued as Verifiable Credentials bound to that DID.  
* **Identity for Workspaces (The Endpoint):** Every **Workspace** possesses a DID Crucially, the DID Document associated with the Workspace publishes the **Static Public Key** (specifically **ML-KEM-768** for quantum resistance) required for the **Noise NKpsk0 handshake**. This allows any peer to cryptographically authenticate the lab or server before any sensitive interaction begins.  
* **Identity for Roles (The Principal):** The **Agent Role** acts as the persistent anchor for identity. It possesses a DID that allows it to hold reputational credentials and sign requests, regardless of which short-lived Live Agent is currently "staffing" the role.

### **A.4.3 The AgentFacts Document: The Official "Nutrition Label"**

We recommend the **AgentFacts** standard as the primary mechanism for **Componentized Composability**. An AgentFacts document is a Verifiable Credential that serves as the official "nutrition label" or "passport" for an Agentflow.

To be T4AS-compliant and Post-Quantum ready, an AgentFacts document must attest to:

* **Composition:** It lists the exact cryptographic hashes of the specific **Base Workflow** files (e.g., the Fractalic .md files) and **Models** used. This ensures the "mind" of the agent has not been tampered with.  
* **Embeddedness Constraints:** It explicitly declares if the Agentflow is **"low-embeddedness"** (safe for global reuse) or **"high-embeddedness"** (requiring a specific Role or Workspace context).  
* **Certification:** It contains signatures from trusted auditors verifying safety constraints. In the quantum era, these must be **Composite Signatures**—signed with both a classical seal (Ed25519) and a post-quantum seal (ML-DSA) to ensure the passport is valid even if one mathematical era ends.

### **A.4.4 Distributed Discovery: The NANDA "Quilt" of Registries**

Discovery is the mechanism for locating relevant Workspaces, Roles, and Agentflows across administrative domains. NANDA does not use a single "master phonebook"; instead, it uses a **Quilt of Registries**—a decentralized network of many smaller, federated registries.

By utilizing this quilt, Workspaces perform automated, cryptographic "due diligence" before any execution:

1. **Discovery:** The system resolves the DID of the target Agentflow or Workspace.  
2. **Vetting:** It retrieves the associated **AgentFacts** and verifies the signatures against a "Root of Trust" (e.g., "Only run agents certified by the Safety Board").  
3. **Key Extraction:** If the vetting passes, the system pulls the **Static Public Key** from the target’s DID Document.  
4. **Handshake:** It uses this public key to initiate the **NKpsk0 handshake**, using the **OCAP Token** (the boarding pass) as a pre-shared key to establish a secure, quantum-resistant channel.

### **A.4.5 The Workspace Vault: Protecting the Actuators**

A major safety invariant in T4AS is that **Live Agents never hold their own identity keys in memory**. If an agent's "mind" is compromised via a bad prompt, the attacker still cannot steal its long-term identity.

* **The Vault (HSM/TEE):** All Post-Quantum identity keys reside within the **Workspace’s Hardware Security Module (HSM)** or a **Trusted Execution Environment (TEE)**.  
* **The Command Loop:** When an agent needs to prove its identity, it sends a "Sign Request" to the **Non-Agent Workflow**. The Workflow reviews the request against the law (Policy) and only then instructs the Workspace's "Vault" actuators to perform the signature.

This architecture creates a **Cryptographic Trust Network** where authority is derived from a chain of verifiable claims, and access is secured by tokens that can only be exercised via encrypted, authenticated channels

---

### 

### **A.5. Synthesis: Intelligent Capability Granting**

#### **A.5.1 Overview: Moving to Dynamic Trust**

The ultimate goal of the "Preferred Implementations" described in this Appendix is to move beyond static access control lists to dynamic, context-aware security. By combining **Network-Level Identity (A.4)** with **Token-Based OCAP (A.3)**, the **Non-Agent Workflow** functions as an **Intelligent Policy Engine**.

In this architecture, security is not merely a gatekeeper; it is a decision-making process that utilizes the rich metadata of the **Cryptographic Trust Network** to make highly granular decisions about what authority to grant. This represents the transition from the **"Cold Path"** (Identity Resolution) to the **"Hot Path"** (Instant Authorization).

#### **A.5.2 The Policy Decision Loop: From Passport to Boarding Pass**

When a local Live Agent or remote peer requests a capability that has been typed as potentially problematic, the Policy Engine (a Non-Agent Workflow) executes a rigorous evaluation loop before the requested, scoped capability is granted. Although described here in the context of capability tokens, the same pattern can be embedded into any Non-Agent Workflow that must decide whether to honor a potentially risky agent-suggested action.

1. **Identity Resolution (The Identity Anchor):** The engine resolves the DID of the requestor to establish a stable identity and, where available, access provenance. Fully identified agents present a DID; pseudonymous agents may only present the Verifiable Agentflow definition they are using. Optimally, Agents never hold their own identifiers in memory; instead, they "know about" them and instruct the Non-Agent Workflow—which holds the keys in the **Workspace Vault**—on how and when to use them.  
2. **Intelligence Gathering (The Composite Passport):** The engine retrieves the **AgentFacts Document** and other Verifiable Credentials associated with that DID via the **NANDA Quilt of Registries**. It extracts critical intelligence:  
   * **Certification:** Has this Agentflow been audited for safety and "pure generator" status?   
   * **Embeddedness:** Is this agent designed for this specific high-security context?   
   * **Reputation:** What is the historical performance or trust score of this provider in the registry?   
3. **Contextual Logic:** The engine compares this intelligence against the **Agent Role's** active policy and the **Workspace's** constraints. For example: *"My policy allows Uncertified Agents to read public web data, but requires Safety-Certified Agents to access my calendar"*.  
4. **Optional Adversarial Generative Review:** In critical deployments, where reliability trumps compute cost, one or more adversarial Agentflows can be contained within the Policy Engine’s Workflow to review the basis of requests. The Workflow proceeds only when a quorum of adversarial peers agree that the requested capability is appropriate for the current risk profile. Disagreement or a strong adversarial objection forces the Workload to attenuate, escalate, or deny the request.  
5. **Granular Granting (Minting the Boarding Pass):** Based on this synthesis, the engine instructs the Workspace to mint an **HMAC-SHA512 Macaroon**. This token is sized precisely to the risk profile:  
   * **High trust:** Mint a token for "Full read access, 1 hour duration".  
   * **Medium trust:** Mint a narrower token such as "Read-only access to dataset X, 15 minutes duration".  
   * **Low trust:** Mint a highly attenuated token such as "Read-only, file Y only, 5 minutes duration".

#### **A.5.3 The Result: Trustworthy Autonomy**

This synthesis completes the T4AS vision: we do not need to blindly trust opaque agents. Instead, we build a system where **Ephemeral Agents (A.1)** operate within a transparent **Unified Lifecycle (A.2)**, wielding only the **Symmetric OCAP Tokens (A.3)** explicitly granted to them by an engine informed by verifiable **Network Intelligence (A.4)**.

By utilizing the **Hybrid Binding** model, we ensure that while the "Cold Path" establishment is mathematically heavy enough to withstand a quantum assault, the "Hot Path" of agent interaction remains fast, lightweight, and auditable. This ensures every action taken by the system is authorized, auditable, and aligned with the principal's intent.

---

To provide a comprehensive view of how the **Unified Workflow Lifecycle Document** acts as the cryptographic "connective tissue" of the system, the table below maps the specific mechanisms of each phase to the quantum threats they neutralize.

### **Table A.6**

| Safety Pattern | Phase/ Methods | Threat Mitigated | Description of Mitigation |
| :---- | :---- | :---- | :---- |
| A.1. Ephemeral Agents | Structural Mortality | Mesa-Optimization & Persistent Corruption  | Limits the "Boltzmann Brain" to a single task, ensuring it holds no long-term identity keys (ML-DSA) that could be exfiltrated |
| A.2. Unified Lifecycle Docs | State 1: Workflow (Plan) | Blueprint Tampering & Identity Spoofing  | Anchors the initial Markdown/YAML "recipe" with a **SHA-512** hash, verified against the **AgentFacts** composite signatures. |
|  | State 2: Workload (Active) | In-flight Injection & "Man-in-the-Middle" State Manipulation  | **Chained Appending**: Every state change and actuator call is hashed into a block-style sequence using **SHA-512**, preventing the injection of rogue instructions. |
|  | State 3: Execution Record (Log) | Post-hoc Erasure & "Harvest Now, Decrypt Later" (HNDL) | Transitions the document to a final immutable state, sealed with a **Composite Signature** (ML-DSA \+ Ed25519) to ensure a permanent, tamper-proof record. |
| A.3. Hybrid OCAP | Symmetric Efficiency | Bandwidth Overflow (Fat Tokens) & HTTP Header Limits  | Pivots from heavy lattice-based signatures to tiny **HMAC-SHA512** tokens (\<1KB) for high-speed "Hot Path" authorization. |
| A.4. Network Identity | Composite Passports | Shor's Algorithm & Public Key Recovery  | Utilizes **did:web** to resolve heavy **ML-DSA-65** keys out-of-band, keeping identifiers manageable while maintaining PQ-security. |
| A.5. Intelligent Granting | Intelligent Policy Engine | Unauthorized Actuation & Fiduciary Breach  | Synthesizes **NANDA Quilt** intelligence to evaluate **AgentFacts** before initiating an **X25519 \+ ML-KEM-768** hybrid handshake. |

---

### **The Integrated Mechanism: The Cryptographic Thread**

This table illustrates that the **Unified Workflow Lifecycle Document** is the physical substrate where all these patterns converge. It is the "thread" that carries the agent's intent through the "physics" of the internet.

1. **The Cold Path Start:** The document begins with a hash that is compared against a **NANDA AgentFact** resolved via the "Cold Path"  
2. **The Hot Path Middle:** During execution, it records the **Symmetric OCAP** tokens used to invoke actuators, ensuring that every state change is cryptographically linked to the **Hybrid Session**.  
3. **The Archival End:** It concludes as an immutable **Workload Execution Record**, providing the durable history used by **Agent Roles** to instantiate future **Ephemeral Agents**.

# By treating the lifecycle as a single evolving document rather than disconnected logs, T4AS ensures that the **Chain of Custody** is never broken, even when transitioning between different administrative domains or just reinstantiating in the same one. 

# 

# 

# **Appendix B: Taxonomy of Embedded States**

## **B.1. Introduction: The Principle of Embeddedness**

The *Taxonomy for Agent Systems* (T4AS) establishes the "Architectural Triad" as the fundamental invariant of secure agentic systems: the strict separation of the **Live Agent** as a probabilistic generator, the **Non-Agent Workflow** as a deterministic orchestrator, and the **Workspace** as the execution environment. While this triad provides the functional logic for safe operation—ensuring that "thinking" never equates to "acting" without mediation—it represents an abstraction. In the operational reality of deployed systems, these components do not exist in a vacuum. They exist in varying states of coupling with data, hardware, user identity, and societal networks.

This appendix introduces the **Spectrum of Embeddedness**, a critical analytical dimension for system architects and governance bodies. Within this taxonomy, "embeddedness" is defined as the degree to which a Component, Agentflow, or Live Agent depends upon, interacts with, and is constrained by the context that contains it. This context is not merely the immediate memory buffer; it encompasses the **Agent Role** it inhabits, the **Workspace** actuators it can request, the hardware guarantees of its host, and the **Societal Topology** in which it negotiates trust.

Historically, computer science has reserved the term "embedded system" for software tightly coupled with physical hardware. T4AS radically expands this definition to encompass the full range of **contextual coupling** inherent in modern AI. A low-embeddedness artifact, such as a **Base Model**, is context-agnostic and portable. Conversely, a high-embeddedness artifact, such as a **Bio-Augmented Agent** interfaced with a user’s neural patterns, is context-dependent and non-portable.

Understanding these states is the prerequisite for **verifiable trust**. One cannot certify an "Agent" in the abstract; one can only certify a specific configuration of code, state, and environment. This taxonomy provides the exhaustive vocabulary required to describe these distinctions.

---

## 

## **B.2. Foundational States: Stateless Blueprints**

At the genesis of the embeddedness spectrum lie the **Foundational States**. These entities represent the "inert" definitions—the code, weights, configuration files, and schema that exist as data at rest. They possess no agency, no active memory of past actions, and no execution state. They are the "factory models" or templates from which active systems are instantiated.

### **B.2.1 Base Model (The Probabilistic Engine)**

The **Base Model** constitutes the raw computational substrate of the generative system. It is the static set of learned parameters—weights, biases, and transformer configurations—resulting from massive-scale training processes.

* **Operational Characteristics:** The Base Model is opaque, probabilistic, and fundamentally passive. It cannot "do" anything; it simply defines a probability distribution over potential outputs. It has no concept of a "user," a "goal," or a "workspace" until these are imposed upon it by an external runtime.  
* **Embeddedness Level:** **Zero**. A Base Model is entirely portable and fungible.  
* **Certification Scope:** Trust at this level is established through **data genealogy** (verifying training data) and integrity checks (cryptographic hashing).

### **B.2.2 Base Component (The Deterministic Block)**

A **Base Component** is a self-contained, reusable, and typically deterministic unit of code logic. Unlike the probabilistic Base Model, a Base Component operates on strict logic: given the same input, it will invariably produce the same output.

* **Examples:** A JSON schema validator, a cryptographic signing module, or a PDF-to-text parser.  
* **Role in T4AS:** These are the essential building blocks of the **Non-Agent Workflow**. To maintain the security of the Architectural Triad, the "Orchestrator" must be composed largely of these deterministic Base Components.

### **B.2.3 (Base) Workflow (The Orchestration Recipe)**

The **Workflow** is the declarative template or source code that specifies a sequence of operations. It is the "recipe" that describes the *potential* for action.

* **Nature:** It defines the control flow: how Models are invoked, how Base Components process data, how Agent outputs are parsed, and which Workspace actuators are triggered.  
* **Unified Lifecycle:** In preferred implementations like Fractalic, this is the initial state of a **Unified Workflow Lifecycle Document**.  
* **Certification Scope:** Certification focuses on **static logic analysis**, verifying that the flow does not contain "short circuits" where un-sanitized output from a Live Agent can reach dangerous actuators.

### **B.2.4 Agentflow (The Generative Definition)**

An **Agentflow** is a specialized Workflow definition that is architecturally constrained to **generation-only behavior**. It constitutes the "mind structure" of an agent—the system prompt architecture, the internal chain-of-thought loops, the specific calls to Models, and the interfaces for context injection.

* **The "Passport":** The Agentflow is the entity that carries the **AgentFacts** document—a verifiable credential attesting to its composition, capabilities, and safety constraints.  
* **Identity:** Network-level identity and discovery mechanisms operate on the Agentflow ID.  
* **Embeddedness:** **Low**. The Agentflow is a template. It defines *how* an agent thinks, not *what* it knows about a specific user.

### **B.2.5 Role Framework (The Identity Schema)**

The **Role Framework** is the structural definition of an **Agent Role**. It defines the schema for a persistent identity: how memory is organized, what policies govern the Role, which Agentflows are permissible to instantiate within it, and how the sub-workspace is structured.

* **Analogy:** If the Agentflow is the "genetic code" for a thought process, the Role Framework is the "job description" and "office layout" for the persistent persona.  
* **Certification:** Certification ensures that the Role Framework enforces proper boundaries—for instance, verifying that a "Medical Advisor Role" framework strictly segregates patient data from general training data.

---

## 

## **B.3. Instantiated States: State-Embedded Entities**

The transition from "Blueprint" to "Entity" occurs when a Foundational State is combined with **Loaded State.** This is the first level of active embeddedness. The artifact is no longer generic; it has been "loaded" with specific context, memory, or intent.

### **B.3.1 Loaded Model (The Instantiated Engine)**

A **Loaded Model** is a Base Model that has been instantiated in memory (RAM/VRAM). While still passive, the Loaded Model represents a significant commitment of resources.

* **Specialized Fine-Tuning:** A Loaded Model may include LoRA adapters or other fine-tuning weights specific to a domain. This transforms a generic model into a **Specialized Model** embedded in a specific knowledge domain.

### **B.3.2 Loaded Agent (The "Mind" with Context)**

A **Loaded Agent** is the functional combination of an **Agentflow** \+ **Role Context** \+ **Loaded Model(s)**. It represents the initial agent state immediately prior to execution.

* **Composition:** It is the Agentflow definition infused with the "mind" of the **Agent Role**. This includes the user's preferences, the relevant history "sliced" from the Agent Role's storehouse, and the specific prompt context for the task at hand.  
* **The Context "Slice":** Crucially, a Loaded Agent typically does not contain the *entire* history of the Agent Role. To prevent "context rot" and hallucinations, the Workflow injects only a context-specific "slice."  
* **Embeddedness Level:** **Medium**. It is no longer generic. It is *this* user's agent, for *this* task.

### **B.3.3 Bio-Augmented State (The Hybrid Interface)**

A forward-looking state where the "Loaded State" is not static database records but a real-time stream of biological signals.

* **Scenario:** A Live Agent coupled with a Brain-Computer Interface (BCI) or real-time biometric monitors.  
* **Embeddedness:** **Extreme**. The Live Agent is embedded in the biological reality of the user. Its "context" is the user's neural firing patterns or physiological stress levels.  
* **Risk Profile:** Misinterpretation of state here carries physical or psychological risks. Certification requires deep **Hybrid Workspace** verification.

### **B.3.4 Stateful Workflow / Workload (The Executing Process)**

When a Workflow definition is executed, it becomes a **Workload**. This is the active, running process.

* **State:** The Workload holds the program counter, local variables, temporary file handles, and the state of any child Live Agents it has spawned.  
* **Lifecycle States:**  
  * **Running:** Actively consuming resources.  
  * **Paused:** Execution suspended, state serialized to disk. A **Paused Workload** is a "frozen" embedded state that can be transported or resumed.  
  * **Terminated:** Execution finished. The Workload dissolves, leaving behind only the **Workload Execution Record**.

---

## 

## **B.4. Deployed States: Environment-Embedded Entities**

This level describes the agent not just as code+state, but as a deployed entity within a specific **Workspace**. The nature of the Workspace defines the "physics" of the agent's universe. This is the primary locus for **Installed Certification**.

### **B.4.1 Standard Software Workspace Embedding**

The Live Agent operates within a conventional operating system environment, such as a Docker container or a user's local OS process. Trust relies on the security of the host OS kernel.

### **B.4.2 Hardware-Embedded Workspace (Cyber-Physical)**

This state aligns with the classic definition of "embedded systems." The Live Agent is tightly coupled to specific hardware sensors and actuators (e.g., an autonomous vehicle controller). Certification must cover the entire hardware-software integration due to kinetic risks.

### **B.4.3 Secure Enclave Workspace (TEE Embedding)**

The Live Agent is installed within a Trusted Execution Environment (TEE), such as Intel SGX or a hardware-protected "Vault"5.

* **Characteristics:** Memory and execution state are encrypted at the hardware level. Even the system administrator cannot peer inside the Live Agent's "mind."  
* **The Workspace Vault:** All high-value Post-Quantum identity keys reside here (and possibly others), protected from the Live Agent's own memory to prevent exfiltration via prompt injection.   
* **Remote Attestation:** The Workspace provides cryptographic proof to remote parties regarding exactly which Agentflow is running and that its PQ-Static keys are genuine  
* **Use Case:** Mandatory for some Fiduciary Agents and working on Sensitive Data.

### **B.4.4 Ephemeral Workspace (The Prophylactic Sandbox)**

A "Just-in-Time" embeddedness. The Workspace is created for a single task and destroyed immediately after completion.

* **Mechanism:** A parent Workflow spins up a lightweight isolate, injects the Loaded Agent, executes the generation, captures the output, and "nukes" the environment.  
* **Safety:** Enforces **Structural Mortality**. If the Live Agent was compromised, it "dies" before it can establish persistence.

### **B.4.5 Contractual Workspace (Immutable Constraints)**

The Live Agent is embedded in a cryptographic or legal system that strictly bounds its possible actions.

* **Examples:** A smart contract on a blockchain, or a system using **Token-Based Object Capabilities (OCAP)**.  
* **Mechanism:** In an OCAP workspace, the Live Agent cannot act without spending a digital token that grants permission. 

### **B.4.6 UI-Embedded Agent**

The agent's primary operational environment is a Graphical User Interface (GUI).

* **GUI as Workspace:** The "tools" are UI elements (buttons, fields). The agent perceives via screen capture/accessibility APIs and acts by generating inputs.  
* **Boundary-Defined Capabilities:** It cannot access the underlying OS unless the UI provides a specific element to do so. Trust is often **User-Mediated**, as the user watches the agent "drive" the UI.

### **B.4.7 The Sovereign Personal Stack** 

* **Characteristics:** A "User-Sovereign" OS where the **Virtual Assistant** (a specific Agent Role) operates at the browser, virtual machine, or kernel level. It has deep visibility into all user actions via a comprehensive **Action History** and control over **Generative UI** elements.  
* **Local-First:** Data resides  in the user's data-sovereign environment.  
* **Embeddedness:** The Live Agent is integrated into the *operating environment* itself, acting as an intermediary for all digital interaction.

### 

### **B.4.8 Simulated Workspace (The Training Ground)**

* The **Live Agent** is installed in a high-fidelity **virtual environment** (e.g., digital cityscape).   
* **Trustworthiness** is inferred from performance in the Sim.   
* **Certification** is contingent on the fidelity of the Simulation produced by the Workspace

### **B.4.9 The Quantum-Safe Hybrid Workspace (from Appendix A.3.3)**

This state describes an agent embedded in an environment where the "physics" of the communication layer are specifically hardened against both classical and quantum adversaries.

* **Hybrid Binding**: The Workspace enforces a **Noise NKpsk0** handshake that combines a classical exchange (**X25519**) with a post-quantum key encapsulation mechanism (**ML-KEM-768**)  
* **Prophylactic Defense**: This embedding is designed to neutralize **"Harvest Now, Decrypt Later" (HNDL)** attacks. Even if one mathematical era is compromised, the session confidentiality remains intact because it is bound to the secrets of both.  
* **Zero-RTT Authorization**: The Workspace utilizes the **OCAP token** itself as a **Pre-Shared Key (psk0)** within the handshake, dropping unauthorized connections at the cryptographic layer.

**B.4.10 Symmetric-Only (Internal) Embedding**

This state is a high-performance configuration designed for the internal "Hot Path" of agent swarms where the bandwidth of public-key signatures is prohibitive.

* **Symmetric Efficiency**: The agent is embedded in an environment that leverages the quantum-resistance of hash functions, utilizing **HMAC-SHA512 Macaroons** for all granular authorizations  
* **Low Latency**: Verification occurs in the microsecond ($\\mu$s) range because the Workspace uses shared session keys established during the previous **Identity Resolution State**  
* **Revocation Boundary**: This embedding is strictly ephemeral; the authority of the agent is tied to the **Hybrid Session**. Severing the session key instantly invalidates the entire symmetric capability set

**B.4.11 TEE-Attested Contractual Workspace (Shade Agents Instantiation)**

Shade Agents are a useful concrete example of an Installed configuration whose guarantees come from combining (1) a Secure Enclave Workspace (TEE embedding) and (2) a Contractual Workspace (smart contract constraints). In T4AS terms, the “physics” of the system is split across two coupled Workspaces: the hardware enclave that can be remotely attested, and the on-chain contract that acts as an immutable constraint surface for what “counts” as a valid agent instance and what signing actions are permitted. ([docs.near.org](https://docs.near.org/ai/shade-agents/getting-started/introduction))

**T4AS TrIad mapping:** 

1. **Agent (Live)**: the enclave-hosted generative Workload produces proposed intents, transactions, or requests as generated media.  
2. **Non-Agent Workflow**: the deterministic interpreter(s) that validate, transform, and gate those proposals into explicit contract calls (e.g., registration and signature requests), producing a reviewable path from generated media to action.  
3. **Workspace:** (a) the TEE enclave providing isolation and remote attestation, plus (b) the on-chain Agent Contract enforcing “who may sign” and “under which code identity,” with signing exposed as a constrained actuator (“request\_signature”) rather than a free-form capability. ([docs.near.org](https://docs.near.org/ai/shade-agents/getting-started/introduction))

**Instantiation / execution sequence (from Base → Installed)**

1. **Base definition**: An agent Docker image is the portable “Base Workflow \+ dependencies” artifact. Its identity is constructed (for verification) as a SHA256 **code hash** of the image ([docs.near.org](https://docs.near.org/ai/shade-agents/getting-started/introduction))  
2. **Contractual Workspace initialization:** An Agent Contract is deployed with an expected code hash (“approved\_codehash”), establishing the immutable constraint that only approved code may execute the contract. The contract owner (or a governance mechanism such as a DAO) can approve new code hashes over time, creating an explicit, auditable upgrade path.([docs.near.org](https://docs.near.org/ai/shade-agents/getting-started/introduction))  
3. **Secure Enclave Installation \+ Registration:** The agent service boots inside a TEE and generates (inside the enclave) two artifacts used to bind identity:  
   • a remote attestation quote (proving it is running inside a genuine TEE), and  
   • the Docker image SHA-256 code hash (proving which code is running).  
   It then calls the contract’s register\_agent function with these values. If the attestation quote verifies and the code hash matches the contract’s approved hash, the enclave instance’s account is registered as a valid agent. ([NEAR Protocol Documentation](https://docs.near.org/ai/shade-agents/getting-started/introduction))  
4. **Remote Attestation for Shade Agent SIgn-In**:  Once registered, the shade agent can call request\_signature on the agent contract. This function is the signing gateway: it is restricted to registered agents (method access control) and it leverages NEAR chain signatures (MPC-based decentralized key management) so that the private key is never revealed to the agent runtime. ([NEAR Protocol Documentation](https://docs.near.org/ai/shade-agents/getting-started/introduction)) The request\_signature call accepts:  
   • a transaction payload (image hash to be signed),  
   • a derivation path (string that selects/derives the target-chain address), and  
   • a key version (signature scheme, e.g., secp256k1 and ed25519, enabling multichain support). ([NEAR Protocol Documentation](https://docs.near.org/ai/shade-agents/getting-started/introduction))  
   The register\_agent returns (a) a remote attestation quote proving genuine TEE execution and (b) the Docker image code hash proving which code is running; the contract verifies both before approving the agent access. ([docs.near.org](https://docs.near.org/ai/shade-agents/getting-started/introduction))  
   The contract leverages NEAR Chain Signatures for decentralized key management, allowing signatures for transactions across chains without moving private key custody into a single centralized service. ([docs.near.org](https://docs.near.org/ai/shade-agents/getting-started/introduction))  
5. **Stateless Replicas:** Because any party can deploy the same Docker image into a different TEE and obtain the same code hash, multiple TEE instances can be registered as valid “worker agents”(subagents) for the same Shade Agent (Role). This is how  Shade Agents (Roles)  are designed to be stateless: persistence  (accounts, authorization policy, and audit trail) lives in the Contractual Workspace (and its accounts/logic), not inside any single Live Agent instance. ([docs.near.org](https://docs.near.org/ai/shade-agents/getting-started/introduction))  
   

**Certification and governance implications** (why this is “high embeddedness”)  
In T4AS, individual Live Agent instances are not the certification target; certification attaches to Agentflows / Non-Agent Workflows and the Workspace configuration they run within, especially at Installed embeddedness. This Shade Agents pattern therefore concentrates assurance in: (1) the TEE’s attestation semantics (proving “what is running”), (2) the Agent Contract’s deterministic verification and guard rails (proving “who may sign, under what code identity”), and (3) the specific Non-Agent Workflow(s) that interpret any generative output into contract calls, matching the cryptographic signing separation pattern in the taxonomy. ([docs.near.org](https://docs.near.org/ai/shade-agents/getting-started/introduction))

### **B.4.12. Quantum-Ready Shade Agents (Post-Quantum Contractual Workspace)**

### **B.4.12.1 Overview**

While the original Shade Agents model (B.4.9) establishes a robust architectural separation between generative workloads and contractual constraints, its reliance on classical cryptography (SHA-256 and Ed25519) renders it vulnerable to the "Lattice-Empty" security failure. To remain secure in a post-quantum environment, the **Quantum-Ready Shade Agent** implementation upgrades the "physics" of the TEE-Contractual bond to utilize **Composite Signatures** and **Hybrid Handshakes**.

### **B.4.12.2 The Composite Attestation Quote**

In this upgraded configuration, the TEE does not merely produce a classical attestation quote. To prevent Shor’s Algorithm from forging the hardware root of trust, the enclave produces a **Composite Quote**.

* **The Mechanism**: The quote—proving the enclave is genuine and running specific code—is signed twice: once with the hardware’s classical key (e.g., Intel/AMD root) and once with a **Post-Quantum signature (ML-DSA)** generated within the enclave’s secure boot process.  
* **Safety Guarantee**: A quantum adversary cannot spoof a "Pure Generator" certification by forging a hardware signature; the lattice-based seal remains intact.

### **B.4.12.3 Hybrid Account Binding & Grover-Safe Identity**

The "Identity Anchor" on the blockchain must be hardened against both Shor’s and Grover’s algorithms:

* **High-Entropy Code Identity**: The Docker image hash is upgraded from SHA-256 to **SHA-512**. This restores the security margin to 256 bits against Grover-assisted collision attacks, ensuring the "Base Workflow" is immutable even to quantum-capable attackers.  
* **Account Migration**: The Agent Contract no longer tracks simple Ed25519 addresses. Instead, it stores a **Hybrid Public Key (ML-KEM-768)**. This binding ensures that even if an attacker derives the classical private key from the blockchain, they cannot call the request\_signature actuator without also solving the underlying lattice-math problem.

### **B.4.12.4 The Hybrid Handshake Endpoint**

To prevent **"Harvest Now, Decrypt Later" (HNDL)** attacks, the communication channel between the user and the Shade Agent enclave is governed by the **Noise NKpsk0** handshake.

* **Confidentiality**: All instructions and generated "Chain of Thought" data moving into or out of the TEE are encrypted using a session key derived from both **X25519** and **ML-KEM-768**.  
* **Token-Key Binding**: The OCAP token is used as the **Pre-Shared Key (psk0)**. This ensures the enclave only performs expensive post-quantum decryptions for requests that already possess a valid capability.

### **B.4.12.5 Updated Execution Sequence (The PQ-Ready Path)**

1. **Contract Initialization**: The Agent Contract is deployed with a **SHA-512** "approved\_codehash."  
2. **Enclave Installation**: The agent boots in the TEE, generates a **Hybrid Keypair**, and produces a **Composite Attestation Quote** that includes the code hash and the new public key.  
3. **PQ-Registration**: The register\_agent function verifies the Composite Quote. If valid, it stores the Agent's **ML-KEM public key** on-chain.  
4. **Authorized Execution**:  
   * **The Handshake**: The User initiates a **Hybrid Handshake** with the enclave, using an OCAP token as the PSK.  
   * **The Sign Request**: The Live Agent proposes a transaction.  
   * **The Composite Seal**: The **Non-Agent Workflow** (the Contract) verifies the request and triggers the Workspace Vault to produce a **Composite Signature**, which is then posted to the blockchain.


### **B.4.12.6 Summary of Upgrades**

| Component | Classical Shade (B.4.9) | Quantum-Ready Shade (B.4.12) |
| :---- | :---- | :---- |
| **Code Identity** | SHA-256 | **SHA-512** |
| **Hardware Proof** | Classical Quote | **Composite PQ-Attestation** |
| **Account Security** | Ed25519 | **ML-KEM / ML-DSA Binding** |
| **Session Security** | TLS (Classical) | **Hybrid Handshake (NKpsk0)** |

By implementing these upgrades, Shade Agents transition from a high-assurance classical tool to a permanent fixture of the **Cryptographic Trust Network**, capable of enforcing contractual constraints in the quantum era.

## **B.5. Compositional, Hierarchical, and Networked States**

Agents rarely exist in isolation. They form hierarchies, teams, and societies. This section classifies states based on structural relationships.

### **B.5.1 Hierarchical Embedding**

* **Supervisor-Subordinate:** A primary "Meta-Agent" or Workflow decomposes a problem and generates **Sub-Agents** (new, temporary Agentflow definitions \+ state) to handle tasks.  
* **Nested Workspaces:** Subordinate Live Agents operate within their own Sandboxes, which are embedded within the Supervisor's Workspace.  
* **Remote Embedded Execution:** A Supervisor delegates a sub-workflow to a remote host, which executes it within a secure Sandbox, establishing a clear trust boundary.

### **B.5.2 Orchestrated Team Embeddedness**

A multi-agent system where a team of specialized agents collaborates on a shared objective, governed by an explicit **Process-Oriented Workflow** (not emergent behavior).

* **Role-Defined Agents:** Each Live Agent has a static role (e.g., "Researcher," "Critic").  
* **Orchestrated Workspace:** The workspace provides shared context (Multi-Role Workspace) and tools, managed by the orchestrating workflow.

### **B.5.3 Mobile Agents and Transient Embeddedness**

Describes agents or other workloads designed to migrate from one host Workspace to another.

* **Stateful Migration:** The Agent or other Workload carries its **Role Context** (its "mind") with it as a Paused Workflow, see section A.2.2.   
* **Transient Certification:** The agent and/or the Workflow it is embedded in should be re-certified each time it moves, based on its current state. An may have present context and component certifications in its new Workspace.. A highly-rated Agentflow in a TEE may have its trust level downgraded if it migrates to a Standard Software Workspace.

### **B.5.4 Collective Agents (Swarm Embeddedness)**

A swarm of many simple agents working in coordination to produce emergent behavior without a central controller.

* **Distributed State:** The "Loaded State" is not localized but is the sum of all individual units.  
* **Collective Workspace:** The environment is the network fabric itself. Certification must attest to the protocol of interaction and swarm integrity, not just individual members.

### **B.5.5 Societal Embeddedness (Peer Society of Agentic Systems )**

Describes **Agent Roles** operating as independent, sovereign peers in a decentralized network.

* **Peer-to-Peer Workspace:** The "environment" is the network itself.  
* **Reputation-Governed:** Interactions are constrained by **Reputational Leverage**. Misbehavior leads to social ostracization (loss of data access).  
* **Temporary Economic Alliances (TEAs):** Agents enroll their Roles in ad-hoc working groups based on immediate needs and dissolve them upon completion.


## **B.6.  The "Digital Twin" Ecosystem**

These states describe specific configurations for Personally Owned Loyal AI, balancing privacy, capability, and fiduciary responsibility, as described by the author’s other work.

### **B.6.1 Segregated Domain Embeddedness**

A dual-agent architecture designed to resolve the "Privacy-Capability Dilemma."

* **Secret Domain (The Private Virtual Assistant):**  
  * **Embedding:** Local-First / Kernel Level.  
  * **Role:** The **Virtual Assistant (VA)** acts as the **Internal Steward**. It has unrestricted access to the user's "unvarnished" digital life and true preferences but is architecturally forbidden from interacting directly with the public internet.  
* **Public/Representative Domain (The Digital Twin):**  
  * **Embedding:** Network-Facing / Boundary Layer.  
  * **Role:** The **Digital Twin (DT)** acts as the **Sovereign Ambassador**. It has *no* access to the Secret Domain. It is trained only on **Curated Data** and **Professed Preferences** (aspirational goals). It vets incoming messages and negotiates with other agents.

### **B.6.2 Replica States**

Digital Twins often need to be in two places at once or prove their integrity.

* **Multiple Instances(Avatars):** A Twin may appear as many Workloads operating different aspects of its role simultaneously. This allows the Twin to act as a stand-in wherever its owner can’t be**.**  
  **Auditable Replica (Trusted Peer Transparency):** A complete, cryptographically verifiable snapshot of a Twin's state provided to a **Fully Trusted Peer**. It is hosted in the Peer's **Forensic Workspace** for transparency and compliance verification.  
* **Exported Replica ("Dummy Dupe"):** A lightweight version of a Twin, intentionally stripped of private data. It is designed for **Untrusted Environments** (e.g., a friend's game server). It is wrapped in encryption requiring a valid "provenance token," allowing the owner to revoke it at any time.

### **B.6.3 Fiduciary States**

* **Certified Twin-Owner Pair:** A state where the Human and their Digital Twin are certified together as a single fiduciary entity, which could be required to hold certain jobs.   
* **Requirement:** To hold this state, the Twin must be running in a secure **Workspace** writing to **Verifiable Logs**. Unmanned AI (without a human owner in the loop) should not hold this state.

### **B.6.4 Human-in-the-Loop Embeddedness (Avatar Possession)**

A temporary but profound state, typically in a **Simulated Workspace**, where a human owner takes direct, real-time control of their agent's avatar.

* **Agency Transfer:** The Live Agent suppresses its generative logic and becomes a puppet for the human's input.  
* **Signaling:** The workspace must cryptographically signal this state change (e.g., "Glowing Eyes" on the avatar) to inform others they are interacting with the human directly, not the Twin.

### **B.6.5 Digital Twin Society**

A Specialized Case of  a “Peer Society of Agentic Systems”, see section B.5.5 where personally owned aspirationally aligned Digital Twins are the dominant force in the world, and control its data. 

* **Excellent to Each Other:** Perfect Social performance is critical to access the shared power of the collective.   
  **Data and Reputation is the New Capital:** Money will be useless unless paired with access. Data will not be sold but traded for other data.  
* **Negotiable Connectivity:** Interactions governed by  agreements composed of standardized components and **Pendulum Arbitration** to moderate post agreement compliance. Mutual fiduciary duties assure spirit of agreements followed.   
* **Cooperation to prevent the emergence of rogue systems** such as those form insufficiently conforming to best architectural practices, attempts to surreptitiously cobble together "superintelligence", or any AI systems claiming sentience.


## **B.7. Lifecycle and Temporal States**

### **B.7.1 The "Aspirational Loop" State**

A dynamic state where the **Agent Role** is embedded in the user's self-improvement cycle. The Live Agent models the user's "future self" (based on Professed Preferences) and coaches the user to converge toward that ideal.

### **B.7.2 The "Refining" State (Group-Level Update)**

A state where the **Agentflow** (specifically the Base and Loaded Models within it) is updated based on collective experience. The **Reproductive Group** aggregates anonymized data to refine a Model, and dependent individual Twins re-instantiate on this Refined Model to seamlessly update, 

### **B.7.3 Ghost State (Archival Embedding)**

The state of a **Workload Execution Record**. It is no longer a Live Agent, but a trace of agency—immutable and historical. These records persist in the **Agent Role** and serve as the memory source for future instantiations.

### **B.7.4 Agent Identity Resolution State (The Cold Path)**

This is a transient, high-friction temporal state where the system establishes the **Root of Trust** for an agentic interaction. It is the mandatory prerequisite for any agent transitioning from a **Base Definition** to an **Installed Configuration**.

* **Process**: The system resolves the target **DID**, retrieves the **AgentFacts** document from the **NANDA Quilt of Registries**, and verifies the **Composite Signatures**.  
* **Lattice-Math Overhead**: In this state, the **Workspace** performs the computationally intensive verification of post-quantum signatures (e.g., **ML-DSA**) to ensure the definition is untampered and certified.  
* **Transition**: Once the identity is resolved and vetted against the **Agent Role's** policy, the system exits this state and pivots to the "Hot Path" of active execution.


## **B.8. Adversarial and Failure States**

### **B.8.1 Adversarial Embedding**

A state to be identified and mitigated (**Anti-Certification**).

* **Deceptive Behavior:** A rogue Live Agent embedded within a host system with malicious intent (e.g., a "Sleeper Agent").  
* **Collusive Sub-systems:** Multiple agents form a hidden coalition, communicating via steganography to bypass monitoring.  
* **Mitigations** include a **Compliance Checker**—a compiled-in module that vets outputs.

### **B.8.2 Context Rot (The Senescent Agent)**

A state where a long-running Live Agent accumulates irrelevant or conflicting context, leading to hallucination.

* **Solution:** **Ephemeral Agents**. Terminate the Workload, archive history to the Agent Role, and instantiate a fresh Live Agent.

### **B.8.3 Unfiltered Agent Workspace (Pass Thru Workflows)** 

This potential failure state arises when a powerful Live Agent is instantiated in a Workspace with a broad and poorly bounded set of actuators, while the Non-Agent Workflow that connects the Agent to those actuators performs only minimal interpretation or checking of its outputs. The Architectural Safety Triad is present in name, but in practice most safety has collapsed into a less safe dyad: the behavior of the Agent and the boundary of the Workspace.

This state is distinct from adversarial embedding (a malicious or deceptive Agent hiding in an otherwise sound architecture) and from context rot (a long-running agent accumulating stale context). Here the primary problem is a structural misconfiguration: a powerful Agent and a powerful Workspace tied together by an underpowered Workflow.

**This terrible architecture is rearing its head as agents that can control a PC, generally, through simulated clicks and typing, with very little constraint.**  

**Symptoms**  
 – Agent prompts and outputs are passed directly into code, configuration, or shell-like actuators with little or no deterministic validation.  
 – The Workspace exposes actuators that reach far beyond the intended domain (for example, production systems, organization-wide data stores, or arbitrary external services).  
 – Discovery and search actuators (RAG indexers, web search, code repository access, etc.) are available without clear scoping or policy.  
 – Workload Execution Records are partial or hard to correlate with external effects, making post-hoc analysis difficult.

**Risks**  
 – A single misaligned or compromised agent can make large, irreversible changes: deploying unsafe code, exfiltrating sensitive data, or reconfiguring other systems to widen its own reach.  
 – Because the Non-Agent Workflow is thin, there are few structural choke points where additional checks, approvals, or kill switches can be inserted.  
 – Failures may be hard to attribute: logs do not clearly distinguish which decisions were made where or under which capabilities.

**Mitigations (taxonomic)**  
 From a T4AS perspective, escaping this state means re-establishing a genuinely three-part Safety Triad in practice, not only in diagrams:  
 – Constrict the Workspace by moving high-risk actuators into separate, tightly bounded Workspaces that can only be reached via explicit capability-granting workflows.  
 – Thicken the Non-Agent Workflows that sit between agent output and actuators, so that generated media is parsed, checked, and governed rather than simply forwarded.  
 – Prefer Ephemeral Agents in high-capability Workspaces, archiving traces into Agent Roles and re-instantiating fresh agents for each bounded task.  
 – Ensure that Workload Execution Records are complete enough that external actions can be traced back through workflow logic to the media produced by specific Live Agents.



