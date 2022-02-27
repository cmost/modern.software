# What is Software Engineering?

An early definition I had been working with for what software engineering meant was something along the lines of:

|   |
|---|
| The organizational and individual practices that can accurately predict and delivering quality and cost |

Some interesting observations:

1. This is a far cry from any job description for Software Engineer jobs.  The reality is that most engineering jobs have very little engineering in them and are better thought of as programmers or technicians.  Actual engineering typically happens at manager levels and above though interestingly those job descriptions also often fail to capture much of the definition above.

2. This defintion explicitly calls out people first (though focusing on the organization and individuals in it).  Most software engineering focus too much on specific recipes without acknowledging that organizational and individual needs vary wildly.

3. The definition focuses a lot more on predictable quality and cost. This predictability is at the heart of what makes it engineering.  The focus of most processes and techniques is on delivering quality without any awareness of the fact that not everything has to be at the same standard of quality and not everything can soak up the same kind of costs.  The main engineering activity is not delivery but accurate planning.  There is no accuracy in planning if delivery is not predictable but the end goal is accurate planning where needed.

4. Quality and cost are rather generic terms.  A lot of software engineering practics focus hard on these.  For instance, operational quality would be the number of nines of availability and the processes to deliver this involve monitoring/alerting etc.

5. Accuracy of prediction involves when (scheduling), what (engineering deliverables) and how (execution roadmap, confidence measures).

Over the last few years, I have been disassitisfied with this because it mentions nothing at all about consumers of the product or the business/sales/support side of the product.  It also hides the complexity of the tradeoffs behind an objective sounding **accurately predict** phrasing whereas the reality is that this is not objective at all.


Here is the modified defintion I have been toying with:

|    |
|----|
| Software engineering is the body of organizational and individual practices that yield high transparency to the tradeoffs involved between costs and quality of prediction and delivery of the organizational goals |

More observations:

1. The definition focuses squarely on trade-offs.  Decisions are no longer purely in the engineering domain but involve the full organization.

2. The main engineering act is the one of providing clarity on the tradeoffs involved in the various outcomes.  For example, organizations may decide that they want to maximize throughput and very little predictability (such as the "Move fast, break things" approach pioneered by Facebook).  Others may decide that the primary tradeoff is extreme predictability in quality and cost (such as a NASA project).

3. The defintion above also accomodates the fact that not everyone would want the same trade-offs.  For example, different engineers have different tradeoffs for accuracy-of-schedule vs throughput.  By focusing on these tradeoffs, it focuses on a shared framework for evaluating options.

4.  The complexity of tradeoffs is still not quite captured: for instance, investing in higher upfront costs can provide better scheduling estimates but this does not adequately capture the non-linear nature of software engineering: small changes to organizational goals often incur exponential damages to costs or quality.  There is an insurance-like aspect to software engineering: some software engineering solutions guard better against this non-linearity than others at some fixed cost and organizations would have to do this tradeoff.

5. A related aspect to the above is the software engineering is predominantly change management unlike other engineering.  The complexity of change management is not reflected well (yet).

|    |
| -- |
| Neither of these definitions provide any specifics on how to achieve this goal. |
