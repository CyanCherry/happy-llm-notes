# CUDA

|        | 对应内存层级  | 调度     | 用途定位                                                 |
|--------|---------------|----------|----------------------------------------------------------|
| Grid   | Global memory | 调用单位 | 和 host 逻辑对接，用来启动 kernel，也就是从 CPU 调用 GPU |
| Block  | Shared memory | 运行单位 | 通过切块处理数据，来允许处理大于可用资源的数据量         |
| Warp   | 寄存器        | 分支单位 | 切块数据内再分块处理，不同 warp 可以运行不同指令分支     |
| Thread | 寄存器        | 运算单位 | 最具体的 worker，接近 CPU 单核                           |

### 计算层级

1. Grid：包含自定义个 block
2. Block：一般包含 8 个warp，代码中指定为 256 个 thread
3. Warp：包含 32 个 thread，32 是常量
4. Thread：执行实际指令的单位

### 内存层级

1. Global memory
    1. 读写连续的 32 bytes 时可以合并读写，提高带宽利用率
    2. 使用 128 bits 读取指令可以减少指令数，特定优化下才会启用
2. Shared memory
    1. 每个 warp 访问 shared memory 需要经过 shared memory bank
    2. 每 32 bits 划分到一个 shared memory bank
        1. 所以对于 4 bytes 数据类型的数组 f，$bank (f[i])=i\bmod32 $
        2. 对于 1 byte 数据类型的数组 s，$bank (s[i])=\frac{i}{4}\bmod32 $
    3. shared memory bank conflict
        1. 通常情况，同一 warp 内的多个 thread 读写同一 bank 的数据会导致访问串行化
        2. 特殊情况，除非如果读取的同位置数据，会有广播机制，不会冲突
        3. 可以理解为，每个warp 访问每个 bank 的过程是串行的，并行要求不同 warp 或不同 bank
3. Register

### 同步机制

1. __syncwarp ()
    1. 仅同步 warp 内的所有 thread
    2. Warp 内有 32 个 thread，范围较小
2. __syncthreads ()
    1. 同步 block 内的所有 thread
    2. Block 内有 8 个 warp，256 个 thread，范围较大