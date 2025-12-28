## **Taxonomy for Agent Systems (T4AS, “tee-four-az”)**

[**stephen.vitka@gmail.com**](mailto:stephen.vitka@gmail.com)                                                **V0.4              December’ 25**

*This following Text is not the [current document of record](https://docs.google.com/document/d/1a-Rn9V4UgtXs9EYniTAyjvG93QfzzenXfUNK3nW_Sss/edit?usp=sharing), which is here:
https://docs.google.com/document/d/1a-Rn9V4UgtXs9EYniTAyjvG93QfzzenXfUNK3nW_Sss/edit?usp=sharing

It does not include the Appendices from that document, either.*

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

- receives media from one Agent as a message;  
- applies whatever structural or policy checks are required;  
-  forwards that message into another Agent’s context as input; and  
-  records the exchange into Workload Execution Records associated with the participating Roles.

These conversations may be unmonitored in the sense that no human is reviewing them in real time, but they should still be recorded and replayable at the Workflow and Workspace level. When communication crosses Workspace or organizational boundaries, it is often implemented by an explicit protocol – for example, an Agent-to-Agent (A2A) messaging fabric. In this taxonomy, an A2A endpoint on the local side is simply another actuator of the Workspace. The Non-Agent Workflow decides when to send, what to send, to which remote Role or endpoint, and how to incorporate any reply. Each such send or receive is a controlled, auditable step; it does not let communicating Agents bypass the Safety Triad. Agents that communicate with no significant deterministic barriers can instead be considered sub-agents within a larger agent rather than “communicating agents”.


**6.3.2 Multi-Workspace System**

A Multi-Workspace System consists of two or more distinct Workspaces designed to interoperate while preserving their own trust boundaries and logs. Common examples include:

-  a private Workspace hosting secret-domain Roles (for example, a local-first personal AI stack), and a public-facing Workspace hosting aspirational or representative Roles (for example, a Digital Twin that others can address directly)  
- a peer multi-user system where each Workspace interacts with others with capabilities derived from a distributed trust network

**These Workspaces may be:**

-  installed and certified together as a bundle (for example, “Private Workspace v3.1 \+ Public Workspace v2.4” as a paired offering), or  
- independently selected, as long as they speak compatible protocols and capability formats.

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

### **7. Network-Level Identity and Discovery**

While this taxonomy provides a logical framework for composing agentic systems, the components and Agents (Live) themselves require a mechanism for **network-level identification, discovery, and verification**. Architectures like the **NANDA quilt registry** that points to verifiable **AgentFacts Documents**, provide a concrete example of the infrastructure that can bring this taxonomy to life on a global scale.

The NANDA Project defines an **AgentFacts Document** as the official, cryptographically signed *passport* for an **Agentflow**, attesting to its composition, certifications, and capabilities as defined within this extensible framework. AgentFacts are issued for Agentflows, not Live Agents, but AgentFlowFacts would have been too cumbersome even if more correct. Many Live Agents, across many Workspaces and Agent Roles, can share the same Agentflow, and therefore reference the same AgentFacts Document, which can in turn, point to a canonical copy of the AgentFlow. 

T4AS does not prescribe a specific identity protocol or registry format, but it does specify *which kinds of things* require stable identity if certification, delegation, and auditability are to make sense across system boundaries:

- Workspaces

- Workflows (including Agentflows)

- Agent Roles

- Live Agents (as instances bound to Agent Roles and Agentflows)

The details of concrete identity mechanisms (e.g., DIDs, DNS-style naming, key-based identifiers, and NANDA-style registries) will be discussed in **Appendix A.**



#### **7.1 Identities for Taxonomic Primitives**

At the network level, T4AS assumes that each of the following can be named, addressed, and referred to in logs, certificates, and registry entries (such as AgentFacts pointers):

* **Workspace Identity**

   A Workspace is a distinct execution environment capable of hosting Workloads and maintaining its own Workload Execution Records.

  * A **Workspace ID** indicates *where* a Workflow or Agentflow is running and *which* trust and policy regime applies.

  * A Workspace identifier should persist longer than any single Live Agent instance that runs within it.

* **Workflow Identity (including Agentflows)**

   Every Workflow that may be called remotely—including Agentflows—has a stable identity as a *definition*.

  - A **Workflow ID** refers to a Non-Agent Workflow definition.

  - An **Agentflow ID** refers to the generative specification from which Live Agents are instantiated.

     Certification, catalogs, discovery, and AgentFacts operate primarily on these **definition-level** identities, not on individual Live Agents, whose trustworthiness derives from the trusted inputs into that Agent.

* **Agent Role Identity**

   An Agent Role is a persistent storehouse of context and Workload Execution Records associated with a particular perspective.

  - A **Role ID** identifies this long-lived container of state and policy.

  - Multiple Live Agents may be instantiated into the same Role over time; they inherit association with the Role ID but are not themselves the anchor of long-term identity.

* **Live Agent Instance Identity**

   A Live Agent is an instantiation of an Agentflow within a Workspace, often bound to an Agent Role.

  * A **Live Agent Instance ID** can be used to trace a particular run or bounded cluster of runs, especially for incident investigation or reproduction.

  * Use of a Live Agent IDs is typically discontinued when the Agent instance ceases execution and is archived, and its use may remain internal to a Workspace. Agent Role and Agentflow identifiers that have associated reputation data and certication usually serve for Workspace to Workspace authenitcation and authorization. 

  * This separation between the identities of *definitions* (Workflows, Agentflows) and *instances* (Live Agents, concrete runs in specific Workspaces) mirrors the distinction between low-embeddedness and high-embeddedness states: stable definitions can be certified globally (and represented by AgentFacts), while embedded instances are certified and governed in context.

#### **7.2 Human and Organizational Anchors**

Agent Roles, Workspaces, and Workflows are ultimately anchored to humans and organizations.

T4AS assumes, without prescribing specific schemes, that:

* **Human or Organizational Principals**

  - control one or more Agent Roles,

  - administer one or more Workspaces, and

  - author, curate, or select Workflows and Agentflows (for which Workflow Facts and AgentFacts and certifications may be issued).

* **Principal–Role Relationships**

  - A single principal may instantiate many Agent Roles (e.g., “Virtual Assistant,” “Professional Co-worker”, “Content Creation Twin”).

  - A Role may be shared or delegated under controlled conditions, but remains anchored to one or more principals for accountability.

The precise identity systems for humans and organizations (e.g., PKI, decentralized identifiers, federated identity providers) are outside the scope of body taxonomy, but are touched on in Appendix A. But, what matters is that there is a consistent way to connect:

principal ⟷ Agent Role(s) ⟷ Workflows / Agentflows ⟷ Workspaces

so that governance, certification, and registry entries (such as AgentFacts) can refer to these relationships explicitly.

#### **7.3 Discovery: Catalogs and Registries**

Once components have identities, they must be discoverable. **Discovery** is any mechanism that lets a Workflow or human operator locate relevant:

- Workspaces (and the kinds of Workflows they host),

- Workflows and Agentflows (including versioned definitions),

- Agent Roles (where appropriate), and

- supporting Components such as tools, Models, and policies.

Common patterns include:

* **Local Catalogs**

  - Within a single Workspace, a registry of callable Workflows, Agentflows, and tools.

  - Indexed by function (e.g., “summarization,” “code review”), by certification status, and by applicable Roles.

* **Network Catalogs / Registries**

  - Shared catalogs that index Workflows, Agentflows, and Workspaces across administrative domains.

  - Support search by capability, certification level, embedding constraints, or policy tags (e.g., “PII-safe,” “export-restricted”).

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

  - whether a remote Workspace is acceptable for a given task,

  - whether a particular Agentflow has appropriate certification (for example, via its AgentFacts Document), and

  - whether embedding constraints (e.g., required Role types, data-sensitivity levels) are compatible.

Discovery information is descriptive, not authoritative: it informs decisions about delegation, capability grants, and routing, but those decisions are ultimately made by Non-Agent Workflows and governance policies.

#### **7.5 Identity, Embeddedness, AgentFacts, and Auditability**

At the network level, embeddedness, identity, AgentFacts, and certification interact in several ways:

* **Identity as a handle on embeddedness**

  * Certification records can attach embeddedness constraints to specific Workflow IDs, Agentflow IDs, Workspace IDs, and Role IDs.

- AgentFacts can describe constraints on an Agentflow, making them visible to discovery and orchestration systems.

- AgentFacts as passports for Agentflows

  * An AgentFacts Document is the cryptographically signed passport for an Agentflow, summarizing its composition, certifications, capability surface, and embeddedness constraints.

  * Multiple Live Agent instances, across many Workspaces and Roles, can refer to the same Agentflow identity and AgentFact, enabling consistent interpretation of what that Agentflow is *supposed* to be and do.

* **Cross-Workspace Workload Execution Records**

  * When Workloads cross Workspace boundaries, their Workload Execution Records can refer to the identities of the remote Workspace, the called Workflow / Agentflow (via its ID and AgentFact), and (when appropriate) the relevant Role.

  * This enables traceability across systems without collapsing everything into a single security or audit domain.

* **Ephemeral vs. Persistent Identity**

  * Ephemeral Live Agents may use short-lived instance identifiers, but they always inherit links to their Agentflow, Role, Workspace, and—where present—the AgentFacts Document for their embedded Agentflow. 

  * Persistent structures such as Agent Roles and Workflows retain long-term identifiers suitable for catalogs, certification, and governance.

By separating *what must have identity* from *how identity is implemented*, T4AS leaves room for multiple concrete identity and discovery systems, while giving them a shared vocabulary for describing which entities they name, how those entities relate, and how they are surfaced for composable, auditable agentic systems. Architectures like the NANDA quilt registry represent one concrete way to instantiate this layer at global scale.



### **8. Certification**

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

   - a certified Non-Agent Workflow (e.g., a **“Certified PDF-to-Text Parser”** – Certification A), and

   - a certified Agentflow (e.g., a **“Certified Summarizer Agentflow”** – Certification B, possibly described by an AgentFact).

2. A new, larger Non-Agent Workflow (e.g., **“Summarize PDF Reports”**) is created to orchestrate these Components:

   - it calls the parser,

   - receives its text output, and

   - feeds that output to the Summarizer Agentflow (via a Live Agent instance).

3. This new, composite Non-Agent Workflow can then receive its own distinct **Certification C**.

   - Certification C attests to the security and reliability of this specific combination.

   - It verifies that the Workflow’s logic correctly and safely interprets the Agentflow’s generated media according to a well-specified interpretation contract.

For example, Certification C might verify that:

- the Workflow is designed to pass the Summarizer Agentflow’s output only to a specific writeFile Component,

- the interpreter treats the Summarizer’s output purely as data, not as executable instructions, and

- the Workflow is not vulnerable to misinterpreting malicious text (e.g., “delete all files”) as a command.

In other words, the trust is not in a Live Agent’s “intent,” but in the Orchestrator’s **rigid, auditable interpretation** of the Agent’s outputs.

#### **8.4 The Chain of Trust**

This **chain of trust** can be extended indefinitely.

A high-level **“Quarterly Report Workflow”** (**Certification D**) might call:

  - the **“Summarize PDF Reports”** Workflow (**Certification C**), and

  - another certified Workflow that handles email distribution (**Certification E**).

Each of these Workflows, in turn, may depend on certified Agentflows, Models, and Workspace Components, each with their own certifications and (optionally) registry entries such as AgentFacts pointers.

Because each Component and composition can be certified in isolation and then reused, organizations can:

- reason about **embeddedness** (Base / Loaded / Installed states) for each certified artifact,

- attach certifications and constraints to stable identities (Workflow IDs, Agentflow IDs, Workspace IDs, Role IDs), and

- compose these certified artifacts into larger systems without losing track of where the guarantees come from.

The result is a robust approach to safety and reliability: complex, distributed agentic systems whose overall trustworthiness is derived from the verifiable trust of their individual certified parts and their certified compositions, rather than from undocumented global assumptions.

---

# 

# **Appendix A: Preferred Implementations**

