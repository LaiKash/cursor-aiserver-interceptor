# MITM Proxy Cursor Debug Script

This is a POC of how to decode on the fly the `aiserver` requests/responses of [Cursor](https://www.cursor.com/en). Take a look to the [cursor-rpc](https://github.com/everestmz/cursor-rpc) Proto files extracted by @everestmz (copy included here for convenience).

# How-To

The setup was done on Windows with MiTMProxy. This is to make use of the [local capture](https://mitmproxy.org/posts/local-capture/windows/) feature and capture the whole Cursor APP. This cannot be run on MacOS atm, as a `brew` installation of MITMProxy is needed to get the `local` interceptor to run, which is incompatible with the PyPI version that is needed for the interception script.

## Installation

### Using uv

1. install [uv](https://docs.astral.sh/uv/)
2. clone this repo with `git clone https://github.com/LaiKash/cursor-aiserver-interceptor`
3. run `uv sync` to create python virtual environment and install dependencies.
4. activate the environment

### Using pip

Create a python virtual environment, activate it and the install the following packages with pip:

- `pip install mitmproxy` (don't install it with the Windows installer)
- `pip install protobuf compiler`

### Alternative installation for protobuf

- Install `protoc` from the zip here: https://github.com/protocolbuffers/protobuf/releases
- The python files are already generated from the `.proto` file. In case you want to generate new ones (because they are incompatible with your protoc version from python): `protoc.exe --python_out=. aiserver.proto`

## Run

- `mitmweb  -s mitm_cursor_proto.py --mode local`
- In MitWeb, go to `File > Install Certificates > Get mitmproxy-ca-cert.p12`. Install the certificate as administrator (double click it and install for the whole machine).
- Close mitweb (ctrl+c it).
- `mitmweb  -s mitm_cursor_proto.py --mode local:Cursor` (this will just intercept Cursor when you run it, feel free to use `--mode local` to intercept all)
- Open cursor and try some tab completion. You can use the `printhelloworld.py` in this repo for testing.

## What to expect

You should see an attempt to parse the `proto` requests/responses that mitweb is receiving, in the terminal, decoded. All those requests/responses are also logged into the `grpc_logs` directory for future inspection. The most interesting one is the `StreamCpp` request, the other ones are usually not giving anything important.

If you see any warning you might want to ignore it for now.

