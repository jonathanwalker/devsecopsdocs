---
title: "Capability Maturity Model"
description: ""
date: 2020-11-16T13:59:39+01:00
lastmod: 2020-11-16T13:59:39+01:00
draft: false
images: []
menu:
  docs:
    parent: "culture"
weight: 40
toc: true
---

The [Capability Maturity Model (CMM)](https://en.wikipedia.org/wiki/Capability_Maturity_Model) is a methodology used to assess and improve the capabilities within a given function. DevSecOps Docs aims to help you improve your maturity through documentation, tutorials, and instructions on how to mature your security program. In which it is crucial you understand CMM to demonstrate value as you are going through the documentation. It can help highlight both your strengths and weaknesses in order to identify where attention is needed the most. This can be a great exercise to get a bigger picture of your program and identify work that can be done with the most significant impact. 

## Resources

There are limited standards available out there but the best one I have found available is DevSecOps Maturity Model(DSOMM) [website](https://dsomm.timo-pagel.de/), [documentation](https://owasp.org/www-project-devsecops-maturity-model/), and [heat map](https://dsomm.timo-pagel.de/circular-heatmap) that is available for others to use. Another excellent resource would be the [DevSecOps Playbook](https://github.com/6mile/DevSecOps-Playbook). I would strongly recommend reviewing these as additional standards for you to reference and assess yourself on. Customizing your own maturity model can be beneficial based on the resources you have available to you, separation of duties, and additions that are specific to your organization. 

## Approaches

An approach you can take is to document a table with the control, an `x` for which level you currently believe your process currently is matured, and a description as to why you feel that is the case. This can help document what processes you currently have, strengths of your process, weaknesses in the design, and give outsiders some knowledge they may not have possessed previously about your current controls. Once you have completed the table, think about how you can potentially update it on a regular basis once new controls are implemented showing an improvement over time. 

### Description

- Control should include the section and short description
- Each level on initial, repeatable, defined, managed, and optimized should have a checkmark/cross/x to identify your current level
- Description should contain a short description about your current process

| Control | Initial | Repeatable | Defined | Managed | Optimized | Description                                           |
|---------|---------|------------|---------|---------|-----------|-------------------------------------------------------|
| 1.1     | x       |            |         |         |           | Processes are ad-hoc, chaotic, and unpredictable.     |
| 1.2     |         | x          |         |         |           | Processes are documented and consistent.              |
| 1.3     |         |            | x       |         |           | Processes are defined and managed.                    |
| 1.4     |         |            |         | x       |           | Processes are measured and controlled.                |
| 1.5     |         |            |         |         | x         | Processes are optimized and integrated.               |

### Location

This should be located within your teams documentation ideally hosted where the rest of the organization keeps theirs. Starting out with a spreadsheet might be ideal, though it is best to have this in a location where others can stumble across it as it demonstrates maturity in your domain. 

### Balance

The levels are subjective by definition and these levels would be best reassessed on an infrequent basis based on the future outlook of the team. 

### Optional

You can further expand this by performing the following to help visualize areas of importance and improvement. 

- Adding an additional columns for weights will help identify controls of greater importance
- Coloring of each level such as red for initial, orange for repeatable, defined as gray, managed as light green, and optimized as green
- It might be ideal to split out control into three different columns numbering section, subsection, and control description
- You can create a total role at the end of each section with `(Weight(1-5) * Level(1-5)) / Total Weight = Total` to give yourself a score in each domain

## Conclusion

Once you are complete you should be using this as a living document. Be sure to update it on a regular basis, re-evaluate the balance once every few years, and can use totals in the optional section to show metrics of improvement over time. In the next section will go over how you can empower operations, security, and development teams to build out a minimum viable DevSecOps program. 
