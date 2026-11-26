# NEB GPU Miner for AMD

A fast GPU miner for **NEB** (Neural Engine Byte) on **AMD Radeon** graphics cards, for **Windows** and **Linux
(Ubuntu)**. It mines on the NEB pools below, or solo against your own NEB node.

- AMD RDNA1, RDNA2, RDNA3, RDNA3.5 and RDNA4 GPUs (RX 5000, 6000, 7000 and 9000 series, Ryzen APUs)
- Pools: Suprnova, InnovLab, K1Pool, AethelBlock, CryoPool (and AriaPool), with a ready start script for each; the pool
  protocol is detected automatically
- Every share is verified on the CPU before it is sent
- Live table with the hashrate now, its average and the 8-hour average, shares, difficulty and pool statistics
- Developer fee: 1 %

## Performance

AMD Radeon RX 9070 XT with the stock power limit (−50 mV), on 34 real NEB block models (heights 6384–6416 and
7660–7681):

| Block type | Average | Range |
|---|---|---|
| Kepler (18 models) | **221 MH/s** | 153 – 360 MH/s |
| TESS (16 models) | **1,469 MH/s** | 1,272 – 1,648 MH/s |
| **Average over time** | **≈ 458 MH/s** | |

Measured with the power limit 20 % below stock (Kepler 207 MH/s, TESS 1,373 MH/s, ≈ 428 MH/s over time) and scaled by
the difference measured between the two settings on the same models (+6.6 % on Kepler, +7.0 % on TESS).

NEB blocks alternate between two models, Kepler and TESS, so the hashrate changes at every block. TESS blocks are found
much faster than Kepler blocks (23 s against 97 s on average over the last 1,000 blocks of the chain), so a rig spends
about 81 % of its time on Kepler blocks: weighted by time, the average is about 458 MH/s. The hashrate also changes
from one model to the next as the network trains them (the range above).

These are the rates of the miner on a model it has fully prepared. Expect the pool's long-term average to be somewhat
lower: the first TESS block after the miner starts, and the moments when a new model arrives, run slower.

Only the RX 9070 XT has been measured so far. Other cards should scale roughly with their number of compute units and
their clock.

## Pools

Each pool has its own start script in the package: `start-suprnova`, `start-innovlab`, `start-k1pool`,
`start-aethelblock` and `start-cryopool` (`.bat` on Windows, `.sh` on Linux). All of them need only your `neb1...`
address: no account, no registration.

| Pool | Address | Website |
|---|---|---|
| Suprnova | `neb.suprnova.cc:7804` | [neb.suprnova.cc](https://neb.suprnova.cc) |
| InnovLab | `neb.innovlab.cc:4370` | [innovlab.cc](https://innovlab.cc) |
| K1Pool | `eu.neb.k1pool.com:3370` | [k1pool.com/pool/neb](https://k1pool.com/pool/neb) |
| AethelBlock | `neb-pool.aethelblock.com:3084` | [aethelblock.com/neb](https://aethelblock.com/neb/) |
| CryoPool | `neb.cryo-pool.com:5557` | [cryo-pool.com/neb](https://cryo-pool.com/neb) |

Look up your address on the pool's website to see your hashrate, your shares and your balance. Each pool pays
automatically once your balance reaches its minimum payout (see its website).

> **Important:** the start scripts come with the **developer's address** already filled in, so that they work as an
> example. Put **your own address** in the script you use, or you will be mining for the developer instead of for
> yourself.

## Requirements

**Windows 10 or 11 (64-bit)**

- A recent AMD Adrenalin driver. The miner uses the HIP runtime that the driver installs (`amdhip64_7.dll`); if Windows
  says this file is missing, update the driver.

**Linux: Ubuntu 22.04 or 24.04 (64-bit)**

- The AMD GPU driver with ROCm 7.2, installed with AMD's `amdgpu-install`:

  ```bash
  sudo amdgpu-install --usecase=rocm
  sudo usermod -aG render,video $USER   # then log out and in again
  ```

  Check with `rocminfo` that your GPU is listed.

**Both**

- An internet connection that reaches `neb.suprnova.cc`, whatever pool you mine on (see Developer fee).
- The miner fills most of the free graphics memory with lookup tables (it leaves 2.5 GB free), so it is best not to use
  the GPU for anything else while mining. `--triples off` uses much less memory, at a lower TESS hashrate.

## How to start mining (step by step)

New to mining? Follow these steps; it takes about ten minutes.

### Windows

1. **Update your AMD driver.** Download the latest Adrenalin driver from
   [amd.com/support](https://www.amd.com/en/support) and install it.
2. **Download the miner.** On this page, open **Releases** (on the right) and download
   `neb-miner-amd-windows.zip`.
3. **Extract it.** Right-click the zip, choose **Extract All...**, and pick a folder, for example
   `C:\neb-miner-amd`. Windows Defender and other antivirus programs often flag crypto miners as "potentially unwanted";
   if the miner disappears or is blocked, add an exclusion for its folder.
4. **Get a NEB address** (skip this if you already have one, starting with `neb1`). Create a wallet with the official
   NEB wallet, `neb-wallet`, from the [official NEB release](https://github.com/SilasEigen/neb/releases): it prints your
   `neb1...` address. Copy it somewhere, and **back up your wallet file** (USB stick, cloud): whoever has it, and its
   password, owns your coins, and if you lose it the coins are gone.
5. **Choose a pool and put your address in its script.** Pick one of the pools above, right-click its script (for
   example `start-suprnova.bat`), choose **Edit** (or open it with Notepad), and replace the address after
   `set ADDRESS=` with **your** address. You can also change `rig1` to any name for this computer. Save and close.
6. **Start mining.** Double-click the script. First the miner tests itself on your GPU for **30–45 seconds** (this
   is normal, it is not stuck); then the live table appears. The first numbers to watch are the hashrate (MH/s) and the
   accepted shares (`A`): if `A` keeps going up, you are mining.
7. **Check your earnings.** Open the pool's website (see Pools) and look up your address.
8. **Stop mining** with `q`, or by closing the window.

### Linux (Ubuntu)

1. Install the AMD driver with ROCm 7.2 (see Requirements above) and check that `rocminfo` lists your GPU.
2. Get a `neb1...` address with the official NEB wallet if you have none (step 4 above). Download
   `neb-miner-amd-linux.zip` from **Releases**, then:

   ```bash
   unzip neb-miner-amd-linux.zip
   cd neb-miner-amd
   nano start-suprnova.sh   # put YOUR neb1... address in ADDRESS, save with Ctrl+O, quit with Ctrl+X
   ./start-suprnova.sh
   ```

   The miner tests itself on your GPU for **30–45 seconds** before the live table appears.

   Replace `suprnova` with `innovlab`, `k1pool`, `aethelblock` or `cryopool` to mine on another pool.
3. Check your earnings on the pool's website.

## Command line

Mining through a pool:

```
neb-miner-gpu --pool HOST:PORT --user NEB_ADDRESS --worker NAME --dataset neb-datasets.bin --gpu-kernel pairs
```

Always pass `--gpu-kernel pairs` and `--dataset neb-datasets.bin` (the file that comes with the miner). Any pool of the
table above works, and also AriaPool (`neb.ariabrain.com:3370`).

| Option | What it does |
|---|---|
| `--pool HOST:PORT` (`-o`) | the pool; `stratum+tcp://HOST:PORT` is accepted too (no TLS) |
| `--user NEB_ADDRESS` (`-u`) | your `neb1...` address, which the pool pays; `ADDRESS.WORKER` also works |
| `--worker NAME` (`-w`) | the name of this rig at the pool |
| `--node HOST:PORT` | solo mining against your own NEB node instead of a pool |
| `--devices 0,1` | the GPUs to use (default: every AMD GPU; the integrated graphics of the CPU is left out when there is a graphics card) |
| `--triples off` | use much less graphics memory, at a lower TESS hashrate |
| `--fee-first` | mine the developer fee first (see below) |
| `--plain` | a status line every 5 seconds instead of the live table (for logs and remote shells) |
| `--view classic` | the dashboard of blocks instead of the live table |
| `--selftest --gpu-kernel pairs --dataset neb-datasets.bin` | test the miner on your GPU and exit |

Press `q` in the live table to quit.

## Developer fee

The miner gives **1 %** of its blocks to the developer, whatever pool you mine on: in every 200 blocks, one Kepler
block and one TESS block are mined for the developer's address on Suprnova, at random positions. During those two
blocks the table shows `dev fee` next to the job; the rest of the time every share is yours. With `--fee-first` the
miner mines the two fee blocks right after it starts and then mines the rest of the 200 blocks for you.

The miner keeps a connection to Suprnova open for the fee. **If it cannot connect to Suprnova twice in a row, the
miner stops** with a message: check your internet connection and that `neb.suprnova.cc` is not blocked, then start it
again.

## Troubleshooting

- **Nothing happens for the first 30–45 seconds.** That is normal: every time it starts, the miner tests itself on
  your GPU before it begins to mine.
- **The hashrate goes up and down.** That is normal: Kepler and TESS blocks alternate, and each model is different.
  Look at the average and at the pool's statistics.
- **"amdhip64_7.dll was not found" (Windows).** Update the AMD driver.
- **"no CUDA device found" / no GPU (Linux).** Check `rocminfo`, and that your user is in the `render` and `video`
  groups.
- **"The developer fee pool could not be reached twice in a row".** The miner could not connect to `neb.suprnova.cc`
  (see Developer fee).
- **The pool rejects the address.** The miner checks the address before it connects; copy it again from your wallet.
- **Out of memory, or the desktop becomes slow.** Close other programs that use the GPU, or add `--triples off`.

## License

This miner is based on the NEB reference implementation, licensed under the Apache License 2.0; see the `LICENSE`
file in the package.
