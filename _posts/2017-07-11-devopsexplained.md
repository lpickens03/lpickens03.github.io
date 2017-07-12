---
layout: history
type: Research
category: Research
tags: [devops, management]
title: DevOps Explained
group: 20th Century
timeline: 2008 - Present
---

DevOps is yet another buzzword that frequently comes up when discussing companies like Amazon, Google, Twitter, Etsy and Netflix. In the technology centric market we live in today, taking months or even weeks to deploy software places you at a significant disadvantage. DevOps was developed as a way to routinely deploy thousands of production changes per day while maintaining reliability, stability and security. This allows companies to innovate and out-experiment competition, beating competitors to the market while increasing customer satisfaction, market share and employee productivity.

DevOps was first coined in 2008 by Patrick Debois and Andrew Shafer and refers to the outcome of applying Lean principles to the IT value stream. The principles are nothing revolutionary. In fact, they are based on sound management processes that have been proven time and again over the last century on processes used for manufacturing and physical goods. It wasn't until 2008 that these principles were first applied to the flow of work through product management, development, test, IT Operations and InfoSec. Many DevOps concepts were first proven in the Agile Community which have shown how smaller highly-trusted teams working with smaller batch sizes leads to more frequent and reliable software releases. However, agile generally stops at development. DevOps takes agile and extends it to the entire IT value stream.

The thought process behind DevOps is pretty straightforward: all of your departments work closely together to help each other, and thus the overall organization, win. The primary goal is to make sure work flows smoothly from the upstream development groups to the downstream groups (IT, InfoSec, QA, etc.). To accomplish this DevOps principles generally follow "The Three Ways".

<h2>The Three Ways</h2>
According to <a href="/book%20reviews/2017/07/06/projectphoenix/"><i>The Phoenix Project</i></a>, the underlying principles that all of the DevOps patterns are derived from can be referred to as "The Three Ways". They are defined as follows:

<ul style="font-size: 120%">
	<li><strong>The First Way</strong> focuses on maximizing the flow of work from left to right stages of work flow (development to IT to the customer). It empasizes having smaller batch sizes and intervals of work while constantly optimizing towards global goals (i.e., organization goals, not local team goals).<br/><br/>

	Pracitices empasized include: 
	<ul>
		<li>Continuous build, integration and deployment</li> 
		<li>Creating environments on demand</li> 
		<li>Limiting work in progress (WIP)</li>
		<li>Building systems and organizations that are safe to change</li>
	</ul>
	</li><br/>

	<li><strong>The Second Way</strong> focuses on building a constant flow of feedback from right to left stages of the work flow. The goal here is to enable faster detection and recovery while preventing problems from happening again.<br/><br/>

	Pracitices empasized include: 
	<ul>
		<li>Stopping the "production line” when builds and tests fail in the deployment pipeline</li> 
		<li>Constantly elevating the improvement of daily work over daily work</li> 
		<li>Creating fast automated test suites to ensure that code is always in a potentially deployable state</li>
		<li>Creating shared goals and shared pain between Development and IT Operations</li>
		<li>Creating pervasive production telemetry so that everyone can see whether code and environments are operating as designed and that customer goals are being met</li>
	</ul>
	</li><br/>

	<li><strong>The Third Way</strong> calls for creating a culture that fosters continual experimentation and risk-taking and understanding that repetition and practice are necessary steps toward mastery.<br/><br/>

	Pracitices empasized include: 
	<ul>
		<li>Creating a culture of innovation, risk taking and high trust</li> 
		<li>Allocating at least twenty percent of development and IT Operations cycles towards non-functional requirements</li> 
		<li>Constantly reinforcing that improvements are encouraged and celebrated</li>
	</ul>
	</li><br/>

	<li><strong>The Four Types of Work</strong>: A key concept behind DevOps is identifying the different types of work which can be assigned to people and making that work visible as existing commitments. If some types of work aren't properly tracked, it is difficult for IT Operations to determine how much capacity is available for new or existing projects.
		<ul>
			<li><strong>Business Projects</strong>: Projects that are tied to business initiatives, includes most development projects</li>
			<li><strong>Internal IT Projects</strong>: Infrastructure or IT Operations projects often intended to support business projects or general improvement projects</li>
			<li><strong>Changes</strong>: Usually changes are generated to support business projects or internal IT projects. Depending on how integrated IT is with business projects, these could all be tracked in the same tool. However, more often than not these are tracked in a separate ticketing tool used only for IT.</li>
			<li><strong>Unplanned Work</strong>: Any operational incidents and problems which cause work that comes at the expense of planned work commitments</li>
		</ul>
	</li>
</ul>

<h2>References</h2>
<a href="/book%20reviews/2017/07/06/projectphoenix/"><i>The Pheonix Project: A Novel About IT, DevOps, and Helping Your Business Win</i>, Gene Kim, et. al., 2013</a>