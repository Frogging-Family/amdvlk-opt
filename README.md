# AMDVLK, AMD's standalone Vulkan driver

Can live next to RADV/amdgpu-pro

 https://github.com/GPUOpen-Drivers/AMDVLK

Start your game/app with either:
- `VK_ICD_FILENAMES=/opt/amdvlk/etc/vulkan/icd.d/amd_icd64.json` for 64-bit
or
- `VK_ICD_FILENAMES=/opt/amdvlk/etc/vulkan/icd.d/amd_icd32.json` for 32-bit


You can also pass `VK_ICD_FILENAMES=/opt/amdvlk/etc/vulkan/icd.d/amd_icd64.json:/opt/amdvlk/etc/vulkan/icd.d/amd_icd32.json` if unsure.


## Build:
```
git clone https://github.com/Frogging-Family/amdvlk-opt.git
cd amdvlk-opt
makepkg -si
```
