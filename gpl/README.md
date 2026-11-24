# Kernel Linux do Zhihe (GPL-2.0)

A imagem do Zhihe (`zhihe-imagem-*.zip` nos Releases) usa o kernel do projeto
[msm8916-mainline/linux](https://github.com/msm8916-mainline/linux), tag **`v6.12.1-msm8916`**
(o mesmo do pacote `linux-postmarketos-qcom-msm8916` do postmarketOS), com as alterações abaixo.

| Arquivo | O quê |
|---|---|
| [`zgamepad-kernel-6.12.1-msm8916.patch`](zgamepad-kernel-6.12.1-msm8916.patch) | todas as alterações no código do kernel |
| [`config-6.12.1-msm8916`](config-6.12.1-msm8916) | configuração (`.config`) usada na compilação |
| [`ufi-zgamepad.dts`](ufi-zgamepad.dts) | device tree da placa com os LEDs corrigidos (GPIO 8 vermelho, 6 verde, 7 azul); substitui `msm8916-thwc-ufi001c.dts` |

## As alterações

1. **`drivers/usb/gadget/function/f_fs.c`** — o FunctionFS passa a aceitar descritores de classe
   vendor (necessário para o descritor 0x21 do controle Xbox 360).
2. **`include/net/bluetooth/l2cap.h`** — `L2CAP_INFO_TIMEOUT` de 4000 para 200 ms: DualShock 3
   paralelos não respondem ao L2CAP Information Request e desistiam da conexão.

## Como compilar

```sh
wget https://github.com/msm8916-mainline/linux/archive/refs/tags/v6.12.1-msm8916.tar.gz
tar xzf v6.12.1-msm8916.tar.gz && cd linux-6.12.1-msm8916
patch -p1 < zgamepad-kernel-6.12.1-msm8916.patch
cp config-6.12.1-msm8916 .config
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- olddefconfig Image.gz modules
```

O DTB dos LEDs: copie `ufi-zgamepad.dts` sobre `arch/arm64/boot/dts/qcom/msm8916-thwc-ufi001c.dts`
e rode `make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- qcom/msm8916-thwc-ufi001c.dtb`.
