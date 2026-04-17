# Agentic AI PM Principles

*Principles I developed building enterprise Agentic AI at ServiceNow 
(2020–present). Written for PMs navigating the shift from 
AI-powered to AI-native product development.*

---

## Principle 1: Autonomy must be earned incrementally — trust is the product

When I launched ServiceNow's first Agentic AI product line, the 
hardest problem wasn't the model. It was enterprise trust.

Large organizations will not hand autonomous decision-making to an 
AI system on day one. Every agentic workflow has an implicit 
"trust ceiling" — the point at which a human must stay in the loop. 
Your job as a PM is not to fight that ceiling, but to raise it 
deliberately over time through demonstrated reliability.

In practice this means:
- **Start with high-frequency, low-stakes actions.** We launched 
  with automated case routing before touching case resolution. 
  Small wins build the organizational muscle to trust AI outputs.
- **Make the AI's reasoning visible.** Black-box automation stalls 
  adoption. When our agents showed *why* they took an action, 
  escalation rates dropped and manager confidence rose.
- **Define the human-in-the-loop checkpoint before you ship.** 
  Not as an afterthought. The governance model is a product 
  decision, not a compliance checkbox.

**The outcome:** Our first agentic product delivered a 40% reduction 
in MTTR. That metric only moved because enterprise customers trusted 
the agent enough to let it act without human confirmation on 80% 
of cases — a trust level we built over three product iterations, 
not one launch.

---

## Principle 2: The hardest agentic problem is the handoff, 
not the task

Every agentic system eventually fails at a boundary between agents, 
between systems, between the AI's world model and ground truth. 
The task itself is usually solvable. The handoff is where things 
fall apart.

I learned this building multi-agent workflows across ServiceNow's 
HR, IT, and case management surfaces. An agent resolving an HR 
case might need to pull employee data from one system, trigger a 
workflow in another, and hand off to a human agent with full 
context intact. Every one of those boundaries is a failure point.

In practice this means:
- **Design for failure at boundaries first.** What happens when 
  the receiving system is down? When context is lost? When the 
  handoff is ambiguous? The happy path is easy. The edge cases 
  define the product.
- **Context is not free.** Each agent in a chain needs enough 
  context to act correctly but passing too much context 
  degrades performance and creates privacy surface area. 
  Ruthlessly scope what each agent actually needs to know.
- **Interoperability is a product problem, not just an 
  engineering problem.** When I worked on cross-provider agent 
  interoperability, the hardest decisions were 
  about trust models and data contracts not APIs. PMs need 
  to own that layer.

**The pattern:** The teams that ship reliable agentic products 
obsess over the seams. The teams that ship brittle demos 
obsess over the capabilities.

---

## Principle 3: Non-determinism changes what "done" means 
for a PM

In traditional software, a feature is done when it does what 
the spec says, every time. In agentic AI, your product will 
behave differently on the same input across runs. That 
fundamentally changes the PM's job.

You are no longer specifying behavior. You are specifying 
*acceptable behavior distributions* and building the 
evaluation infrastructure to measure them.

This is the hardest mindset shift for PMs coming from 
deterministic software backgrounds. We had to rebuild how 
we wrote acceptance criteria, how we defined launch readiness, 
and how we communicated risk to executives.

In practice this means:
- **Ship with evals, not just tests.** A passing unit test 
  tells you the code ran. An eval tells you the AI behaved 
  well across the distribution of real inputs. These are 
  different tools for different problems.
- **Define your failure modes before your success metrics.** 
  For every agentic feature, I ask: what's the worst thing 
  this agent could do? How often is acceptable? That 
  conversation with engineering and legal before launch 
  has saved multiple near misses.
- **"Good enough" is a legitimate product decision — 
  name it explicitly.** A 92% success rate on autonomous 
  case resolution might be exactly right for one enterprise 
  customer and completely unacceptable for another. The PM 
  must own that threshold decision.

**The reframe:** In agentic AI, the PM's job is not to specify 
the product. It is to specify the standards by which the 
product judges itself.

---

*These principles are living — updated as the field and my 
experience evolves.*

*Srishti Gupta — Senior Staff PM, GenAI & Agentic AI*  
*[LinkedIn](http://www.linkedin.com/in/srishtiguptaaiproductmanager)*

