# Hurryup Agent - Java version (DEPRECATED)
This is the repository for the deprecated Hurryup Agent, written for elasticsearch 2.4 on ARM Juno board.

The Hurryup Agent is a function-level scheduler for elasticsearch tasks. The basis of this is that some applications has a more intensive function 

## How it is designed to work?
In a high-level discussion, it works dynamically through the real-time profiling of applications (in the concrete case, elasticsearch). By checking the hottest functions and its critical path, the scheduler is able to determine which threads needs more priority in heterogeneous systems and allocate them accordingly.

## How do I install?
Simply clone the repository and use the start.sh script included for a quick start.

## What is the expected overhead of the scheduler?
The expected introducted overhead by running the profiling application is expected to be very high (hence why we dropped the project).

## How the file directory is structured?
* **data**: The collected data until now.
* **docs**: Contains related info for eventual brainstorms that happened during the years..
* **graphs**: Data from the experiments in the juno board.
* **elasticsearch**: Alterations to elasticsearch compilation code.
* **java-agent**: Instrumentation agent for Elasticsearch (Needs ASM and Thread Affinity).
* **energymeasure**: Library for measuring Juno energy for C and Java.

