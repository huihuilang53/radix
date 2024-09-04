gem5正常仿真
#!/bin/bash
./build/RISCV/gem5.opt \
    -d /home/chun/gem5/coremark/meshxy/radix \
    ./configs/deprecated/example/se.py \
    -n 16 \
    --cpu-type TimingSimpleCPU  \
    --l1d_size=64kB \
    --l1i_size=64kB \
    --num-l2caches=16 \
    --l2_size=128kB \
    --num-dirs=16 \
    --ruby \
    --network=garnet \
    --topology=Mesh_XY \
    --mesh-rows=4 \
    -c /home/chun/coremark/coremark-pro/builds/linux64/gcc64-riscv/bin/radix2-big-64k.exe



1、采样(使用gem5)
build/RISCV/gem5.opt \
-d /home/chun/gem5/coremark/meshxy/radix \
configs/deprecated/example/se.py \
--cpu-type=NonCachingSimpleCPU \
-c /home/chun/coremark/coremark-pro/builds/linux64/gcc64-riscv/bin/radix2-big-64k.exe \
--simpoint-profile --simpoint-interval 10000 \
3秒

2、分析生成simpoints和weights(使用simpoint3.2)
./simpoint -maxK 30 -numInitSeeds 1 -loadFVFile /home/chun/gem5/coremark/meshxy/radix/simpoint.bb.gz -inputVectorsGzipped -saveSimpoints /home/chun/SimPoint.3.2/output/radix2.simpoints -saveSimpointWeights /home/chun/SimPoint.3.2/output/radix2.weights

3、生成checkpoints（使用gem5）
build/RISCV/gem5.opt \
-d /home/chun/gem5/coremark/meshxy/radix \
configs/deprecated/example/se.py \
--take-simpoint-checkpoint=\
/home/chun/SimPoint.3.2/output/radix2.simpoints,\
/home/chun/SimPoint.3.2/output/radix2.weights,\
10000,1000 \
-n 16 \
--cpu-type AtomicSimpleCPU  \
--l1d_size=64kB \
--l1i_size=64kB \
--num-l2caches=16 \
--l2_size=128kB \
--num-dirs=16 \
--ruby \
--network=garnet \
--topology=Mesh_XY \
--mesh-rows=4 \
-c /home/chun/coremark/coremark-pro/builds/linux64/gcc64-riscv/bin/radix2-big-64k.exe

4、重载(使用gem5)
build/RISCV/gem5.opt \
    -d /home/chun/gem5/coremark/meshxy/radix \
    ./configs/deprecated/example/se.py \
    -n 16 \
    --cpu-type TimingSimpleCPU  \
    --l1d_size=64kB \
    --l1i_size=64kB \
    --num-l2caches=16 \
    --l2_size=128kB \
    --num-dirs=16 \
    --ruby \
    --network=garnet \
    --topology=Mesh_XY \
    --mesh-rows=4 \
    --restore-simpoint-checkpoint -r 4 \
    --checkpoint-dir /home/chun/gem5/coremark/meshxy/radix \
    -c /home/chun/coremark/coremark-pro/builds/linux64/gcc64-riscv/bin/radix2-big-64k.exe

















