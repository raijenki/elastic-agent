# Hurryup Agent - Java version (DEPRECATED)
This is the repository for the deprecated Hurryup Agent, written for elasticsearch 2.4 on a ARM Juno board (big.LITTLE processors) with JDK8 support.

The Hurryup Agent is a function-level scheduler for elasticsearch tasks. The basis of this is that some cloud applications have a more cpu-intensive function that is important to reach deadline. Whenever a thread accesses that specific function, it (the thread) may be migrated to a more powerful core while within the function. This theorically save us energy consumption by maintaining the biggest part of the application in a lower energy level. 

## How it is designed to work?
In a high-level discussion, it works dynamically through the real-time profiling of applications (in the concrete case, elasticsearch). By checking the hottest functions and its critical path, the scheduler is able to determine which threads needs more priority in heterogeneous systems and allocate them accordingly.
At the moment, the definition of the cpu-intensive function is done manually. The scheduler uses the ASM lib to connect with the OS layer and promove the migration between big.LITTLE cores.

## What the current code does and why was it dropped?
First, the actual overhead is pretty big. Using an external agent, like 'iovisor/bcc', requires the use of the '-XX:+ExtendedDTraceProbes' flag to expose all Java Virtual Machine's symbols on userspace, which is more suited to be more a debugging tool than a tracer.
When using an internal agent, the issue is the need to disable all JVM's security mechanism in order to access processor-level features. Even so, this introduces a lot of complexity (e.g.: java threads are not equal to system threads) and even more overhead because, well, it's JVM. 
Finally, the planned scheduling heuristic (ref. 'docs/Master's Proposal Text.pdf') was for a situation where the number of cores equals to the number of threads. This is not a real condition, as Elasticsearch has more threads than cores on its normal usage. Devising an optimal heuristic for such case required more empirical and application-specific studies that we couldn't afford at that moment.

# What is left to do?
The actual code is able to measure energy consumption within a function. It performs a round-robin allocation within the CPU cores in order to maintain control of threads, tracks execution time and allocates 1 thread per core (either on big or little).
Heavy optimizations with agent code to reduce overhead (maybe using JNI), improving the scheduling heuristics and automating the profiling job is necessary.

## How the file directory is structured?
* **data**: Some collected data. Usually related to variation of keyword length or service time of scheduled / pure (no modification) approaches.
* **docs**: Contains the Thesis Proposal for my master's degree.
* **elasticsearch**: Alterations to elasticsearch 2.4.x compilation code. The 'elasticsearch/src/' folder includes a modification where the 'executeQueryPhase' function (actual hot function) produces a log archive. The 'elasticsearch/bin/agent.txt' is the file that contains the function to be instrumented. 
* **energymeasure**: Library for measuring Juno energy within the JVM. It uses Java Native Interface for that purpose.
* **graphs**: Data from the experiments in the Juno board. Includes studies regarding keylengths, energy consumption and service time within heterogeneous cores.
* **misc**: Some scripts, like the activation of instrumentation agent, a load generator for Elasticsearch (which follows Zipfian's distribution), some attempt of scheduling code in C++.
* **scheduler**: Instrumentation agent for Elasticsearch (Needs ASM and Thread Affinity libs). More information is within the folder's Readme.

## Acknowledgements
* Denilson Amorim (Federal University of Bahia - Undergraduate Student)
* Rajiv Nishtala (Norwegian University of Science and Technology - Postdoctoral Researcher)
* Vinicius Petrucci (University of Pittsburgh - Visiting Scholar) 
The Juno board infrastructure was originally provided by the Barcelona Supercomputing Center, with the support of Professors Xavier Martorell and Paul Carpenter.