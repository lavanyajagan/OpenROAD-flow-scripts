# OpenRoad Flow Understanding and Steps

The PD flow steps are references from [Doc link](https://openroad-flow-scripts.readthedocs.io/en/latest/user/BuildWithDocker.html#build-docker-image) and installed as below:
```
$ git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
$ cd OpenROAD-flow-scripts
$ sudo ./etc/DependencyInstaller.sh        # Script was modified for lemon installation
$ 
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

 
## License

The OpenROAD-flow-scripts repository (build and run scripts) has a BSD 3-Clause License.
The flow relies on several tools, platforms and designs that each have their own licenses:

- Find the tool license at: `OpenROAD-flow-scripts/tools/{tool}/` or `OpenROAD-flow-scripts/tools/OpenROAD/src/{tool}/`.
- Find the platform license at: `OpenROAD-flow-scripts/flow/platforms/{platform}/`.
- Find the design license at: `OpenROAD-flow-scripts/flow/designs/src/{design}/`.
