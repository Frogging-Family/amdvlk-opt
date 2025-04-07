# AMDVLK, AMD's standalone Vulkan driver

This version can live next to RADV and/or amdgpu-pro as it's being installed to /opt

 https://github.com/GPUOpen-Drivers/AMDVLK

Start your game/app with either:
- `VK_DRIVER_FILES=/opt/amdvlk/etc/vulkan/icd.d/amd_icd64.json` for 64-bit
or
- `VK_DRIVER_FILES=/opt/amdvlk/etc/vulkan/icd.d/amd_icd32.json` for 32-bit


You can also pass `VK_DRIVER_FILES=/opt/amdvlk/etc/vulkan/icd.d/amd_icd64.json:/opt/amdvlk/etc/vulkan/icd.d/amd_icd32.json` if unsure.


## Build:

Two versions of this package are offered:
- "prebuilt", which only repackages AMD's binary releases into a pacman package
- "buildfromsource", which involves building the binaries from source


For "prebuilt":
```
git clone https://github.com/Frogging-Family/amdvlk-opt.git
cd amdvlk-opt/amdvlk-prebuilt
makepkg -si
```


For "buildfromsource":
```
git clone https://github.com/Frogging-Family/amdvlk-opt.git
cd amdvlk-opt/amdvlk-buildfromsource
makepkg -si
```
