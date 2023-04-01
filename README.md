# FINAL Submission

The ORFS Docker setup was used to generate the RTL2GDS automation for 1 existing and 2 new designs related to RISC-V (``SHAKTI RISC-V`` and ``SHAKTI FPU``).

- SHAKTI CCLASS routing pic

<img src="https://user-images.githubusercontent.com/35677601/229266649-2adab9cf-f464-49a7-9308-13f5de90b765.png" width=300>

```
==========================================================================
finish slack div critical path delay
--------------------------------------------------------------------------
0.284618

==========================================================================
finish report_power
--------------------------------------------------------------------------
Group                  Internal  Switching    Leakage      Total
                          Power      Power      Power      Power (Watts)
----------------------------------------------------------------
Sequential             1.55e-02   1.81e-03   3.89e-06   1.73e-02  31.7%
Combinational          1.39e-02   2.33e-02   2.75e-05   3.72e-02  68.3%
Macro                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Pad                    0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
----------------------------------------------------------------
Total                  2.94e-02   2.51e-02   3.13e-05   5.45e-02 100.0%
                          53.9%      46.0%       0.1%

==========================================================================
finish report_design_area
--------------------------------------------------------------------------
Design area 29179 u^2 44% utilization.

Elapsed time: 8:40.69[h:]min:sec. CPU time: user 506.23 sys 7.61 (98%). Peak memory: 5673056KB.

```

- ibex

<img src="https://user-images.githubusercontent.com/35677601/229236612-3476f998-8fe3-40ab-883a-eb93fadc23d2.png" width=300>


- SHAKTI FPU

<img src="https://user-images.githubusercontent.com/35677601/229235524-ebf89a49-5915-4ce8-95a7-d795d4140255.png" width=300>


The results are attached as tar files: [results](https://github.com/lavanyajagan/OpenROAD-flow-scripts/blob/master/results.tar.gz)


# OpenRoad Flow Understanding and Steps

The PD flow steps are references from [Doc link](https://openroad-flow-scripts.readthedocs.io/en/latest/user/BuildWithDocker.html#build-docker-image) and installed as below:
```
$ git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
$ cd OpenROAD-flow-scripts
$ sudo ./etc/DependencyInstaller.sh        # Script was modified for lemon installation
$ ./build_openroad.sh 

### Intermediate step 
 $ DOCKER_BUILDKIT=0 ./etc/DockerHelper.sh create -target=builder -threads=4
```


## Changes made - threads=4
```
diff --git a/build_openroad.sh b/build_openroad.sh
index 419c49ba..0d0ba25c 100755
--- a/build_openroad.sh
+++ b/build_openroad.sh
@@ -193,7 +193,7 @@ done

 if [[ "$PROC" == "-1" ]]; then
         if [[ "$OSTYPE" == "linux-gnu"* ]]; then
-                PROC=$(nproc --all)
+                PROC=4
         elif [[ "$OSTYPE" == "darwin"* ]]; then
                 PROC=$(sysctl -n hw.ncpu)
         else
```
## Changes made - lemon installation
```
diff --git a/etc/DependencyInstaller.sh b/etc/DependencyInstaller.sh
index 336c4d0f0..870d03443 100755
--- a/etc/DependencyInstaller.sh
+++ b/etc/DependencyInstaller.sh
@@ -97,7 +97,7 @@ _installCommonDev() {
     lemonPrefix=${PREFIX:-"/usr/local"}
     if [[ -z $(grep "LEMON_VERSION \"${lemonVersion}\"" ${lemonPrefix}/include/lemon/config.h) ]]; then
         cd "${baseDir}"
-        wget http://lemon.cs.elte.hu/pub/sources/lemon-${lemonVersion}.tar.gz
+        wget https://web.archive.org/web/https://lemon.cs.elte.hu/pub/sources/lemon-${lemonVersion}.tar.gz
         md5sum -c <(echo "${lemonChecksum}  lemon-${lemonVersion}.tar.gz") || exit 1
         tar -xf lemon-${lemonVersion}.tar.gz
         cd lemon-${lemonVersion}
```
# The Flow
- Tool setup
- IBEX RISC-V design chosen in Verilog
- Synthesis using Yosys and ABC
- Floorplaning using Replace and Capo
-  Placement using tools in OpenRoad
-   Routing using Fastroute/TritonRoute
-   Layout verification using Magic
-   GDSII using Magic, KLayout

# IBEX Flow

```
$ cd flow
$ export DESIGN_CONFIG=./designs/asap7/ibex/config.mk
$ make
...

[INFO] Writing out GDS/OAS 'results/asap7/ibex/base/6_1_merged.gds'
Elapsed time: 0:04.67[h:]min:sec. CPU time: user 3.80 sys 0.38 (89%). Peak memory: 418052KB.
cp results/asap7/ibex/base/6_1_merged.gds results/asap7/ibex/base/6_final.gds
Log                       Elapsed seconds
4_2_cts_fillcell                 116
3_3_place_gp                    3104
5_1_fastroute                   1927
1_1_yosys                       5875
6_1_merge                        307
3_1_place_gp_skip_io             490
3_2_place_iop                    106
2_3_tdms_place                   100
2_6_pdn                          121
2_5_tapcell                       98
4_1_cts                         5401
3_4_resizer                     1046
2_1_floorplan                    326
3_5_opendp                       443
5_2_TritonRoute                49846
2_4_mplace                       102
6_report                        3725
2_2_floorplan_io                 104
```
## License

The OpenROAD-flow-scripts repository (build and run scripts) has a BSD 3-Clause License.
The flow relies on several tools, platforms and designs that each have their own licenses:

- Find the tool license at: `OpenROAD-flow-scripts/tools/{tool}/` or `OpenROAD-flow-scripts/tools/OpenROAD/src/{tool}/`.
- Find the platform license at: `OpenROAD-flow-scripts/flow/platforms/{platform}/`.
- Find the design license at: `OpenROAD-flow-scripts/flow/designs/src/{design}/`.
