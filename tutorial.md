# Tutorial

In this tutorial you will mount several attacks on a vulnerable implementation
of ECDSA running on a [ChipWhisperer-Nano](https://rtfm.newae.com/Capture/ChipWhisperer-Nano/) board.


## 1. Building the target implementation

Run `make` and observe several `micro-ecc-CWNANO` files being built.

The makefile has one important define `uECC_LEAKY`.
When set to `2` a very leaky scalar-multiplication algorithm is used in ECDSA
(see the [uecc/uECC_leak_more.c](uecc/uECC_leak_more.c) file). When set to `1` a leaky
scalar-multiplication algorithm is used in ECDSA (see the [uecc/uECC_leak.c](uecc/uECC_leak.c) file).
When set to `0` a non-leaky one is used (see the [uecc/uECC_noleak.c](uecc/uECC_noleak.c) file).

The implementation is based on [micro-ecc](https://github.com/kmackay/micro-ecc),
the only modifications are in the mentioned files, you can compare the leaky and non-leaky files to
see the differences.

## 2. Interacting with the target

The [client.py](notebooks/client.py) file has a `DeviceTarget` class
that can communicate with the target. The [utils.py](notebooks/utils.py)
has some utility functions, such as the simple PRNG used by the implementation.

Activate the virtualenv where you have installed the dependencies
(or the pre-created one in the VM):

	. virt/bin/activate

Start the Jupyter notebook server by running:

	jupyter notebook

inside this repository.

Open the [collect.ipynb](notebooks/collect.ipynb) Jupyter notebook.

Connect the ChipWhisperer-Nano board via USB and run the cells in order
to first flash the built implementation on the board, connect to it and then collect
**10** traces.

Use the plots to observe the start of the ECDSA signing process.
The function `EccPoint_mult` is called at around sample index 1100 (when
the `uECC_leak_more.c` implementation is used) and continues way past the
collected amount of samples (the full signing would take around 8 million samples).

Compare `traces[0]` and `traces[2]` using the `plot_traces` function.

Don't forget to disconnect from the target once done, so that other notebooks
can work with it.

## 3. Running the nonce-reuse attack

Open the [nonce_reuse.ipynb](notebooks/nonce_reuse.ipynb) notebook
and solve the TODOs to mount the nonce-reuse attackand extract the private key.
Note that in this case, you can interact with the target in any way using the
methods on the `DeviceTarget` class that are shown in the notebook.

## 4a. Running the nonce-bitlength-leak attack (via timing)

For the nonce-bitlength-leak attacks you will need to collect several thousand
traces, this can take some time. Collecting a 1000 traces should take
about 10 minutes. Depending on the noise level in timing measurement (USB jitter,
VM stuff, overall system noise) from 900 to 3000 traces are required for the attack.
If you do not have the time to collec the traces, use the trace set provided below:

**TODO: Trace set link.**

Open the [nonce_bitlength_leak.ipynb](notebooks/nonce_bitlength_leak.ipynb) notebook.


## 4b. Running the nonce-bitlength-leak attack (via power-tracing)


Open the [nonce_bitlength_leak.ipynb](notebooks/nonce_bitlength_leak.ipynb) notebook.