---
title: "Welcome to EvoAl"
permalink : /
layout: splash
author: Bernhard J. Berger
author_profile: true
classes: wide

feature_row:
  - title: "Applied Researcher"
    excerpt: "*EvoAl* uses domain-specific languages for specifying all aspects of
      optimisation. Starting with data definition, problem specification, algorithm
      configuration, and ending at surrogate learning. *EvoAl* reduces the programming
      effort to zero by using these domain-specific languages. *EvoAl* supports you
      in the configuration by validating the requirements of algorithms against your
      data. This is important to make sure that the algorithm can produce the best
      results possible."
    image_path: "/assets/images/applied.png"
    alt: "Applied researcher pictogram."
  - title: "Optimisation Researcher"
    excerpt: "*EvoAl* supports different optimisation algorithms, community benchmarks,
      and surrogate function learning. It is designed to explicitly state all configuration
      parameters along the experimentation setup, thus, making all decisions transparent
      and supporting experiment replication, which is helpful if you want to earn an [ACM artifact badge](https://www.acm.org/publications/policies/artifact-review-and-badging-current).
      On the algorithm side, *EvoAl+ supports evolutionary algorithms, evolutionary strategies,
      and particle swarm optimisation and the list is continuously enhanced. Furthermore,
      *EvoAl* takes care of training surrogate functions, either on real-world data or
      benchmarks, encoding your data for the optimisation algorithms, and determining useful
      metrics for statistical evaluation." 
    image_path: "/assets/images/optimisation.png"
    alt: "Optimisation researcher pictogram."
  - title: "EvoAl Contributor"
    excerpt: "*EvoAl* has a component architecture with well-defined interfaces and allows a
    DSL-based extension of *EvoAl* DSLs. The component architecture allows anyone to benefit
    from new components provided by the community. *EvoAl* uses dependency injection internally
    to wire all components at runtime based on the provided DSL-based configuration. As
    extensibility and maintainability are in focus during development, you can easily add new
    components to *EvoAl* and extend the domain-specific language. *EvoAl* users can simply use
    your extension by copying your *plugin* into an *EvoAl* release. *EvoAl* automatically
    enhances the DSLs and uses the components when requested by the user. You will find the
    architecture documentation in the Wiki."
    image_path: "/assets/images/contributor.png"
    alt: "EvoAl contributor pictogram."

#url: "#test-link"
#btn_label: "Read More"
#btn_class: "btn--inverse"

# 

---

{: .text-justify}

# Welcome to the home of *EvoAl*.

*EvoAl* is a tool suite that focuses on making optimisation easier to use while
it checks the correctness of configurations. Thus, we aim to make optimisation
algorithms and research accessible to applied researchers and domain experts.
As *EvoAl* and this site are still under construction, there is room for
improvement. At the moment, you find more information on our
[GitLab project]((https://gitlab.informatik.uni-bremen.de/evoal/source/evoal-core))
and its Wiki, which is hosted at the [University of Bremen](https://www.uni-bremen.de).
EvoAl is an open-source research software that is used in several academic
publications, which you find on the [publications page](pages/publications). 

{% include feature_row %}