# linux-cpp-llama-rpc-characterization
This project documents the setup, benchmarking, and performance characterization of Llama.cpp on a Linux-based platform, including local CPU thread-scaling experiments and RPC-enabled execution over Ethernet between two Linux nodes.

# Goal 
The goal of this task is to build llama.cpp on linux, and characterize inference performance on linux with and without RPC. Two linux based computers were put in a direct ethernet network with each other, and RPC was enabled on the network. The inference performance of Llama was measured in presence of RPC and results were noted.
The goal was to establish a local inference baseline, measure prefill and decode throughput, and evaluate the effect of RPC/network overhead on Llama inference performance.

# Benchmarking methodology
From the shell, I ran
for t in 1 2 3 4 5 6 7 8; do
    echo "=== t = $t ==="
    ./llama-bench -m "$MODEL" -t $t -p 128 -n 256 -r 5
done
_______
Here, 
- `-t`: number of CPU threads
- `-p 128`: prompt/prefill token count
- `-n 256`: generated/decode token count
- `-r 5`: number of benchmark repetitions

## Local Benchmark Results
Note: This is the result from one run only. 
| Threads | Prefill pp128 t/s | Decode tg256 t/s |
|---:|---:|---:|
| 1 | 11.91 ± 0.14 | 4.53 ± 0.02 |
| 2 | 22.71 ± 0.41 | 7.25 ± 0.01 |
| 3 | 29.80 ± 0.58 | 7.24 ± 0.00 |
| 4 | 37.16 ± 0.67 | 7.42 ± 0.01 |
| 5 | 34.00 ± 0.46 | 7.12 ± 0.02 |
| 6 | 35.89 ± 0.82 | 7.34 ± 0.01 |
| 7 | 33.45 ± 0.73 | 7.36 ± 0.01 |
| 8 | 35.76 ± 0.79 | 7.04 ± 0.03 |

## Key Observations
- Decode throughput plateaued around 2–4 threads.
- Prefill throughput scaled better than decode throughput.
- Four threads appeared to be near the practical local optimum for this workload.
- Additional threads did not meaningfully improve decode throughput.

## RPC Observation
In this setup, RPC-enabled execution showed lower throughput than local execution. The result suggests that network overhead and RPC coordination can affect real-time inference latency, even when token throughput appears relatively close.

