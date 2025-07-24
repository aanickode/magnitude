<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [infra/README.md](https://github.com/aanickode/magnitude/blob/main/infra/README.md)
- [infra/moondream.py](https://github.com/aanickode/magnitude/blob/main/infra/moondream.py)

</details>

# Deployment and Infrastructure

## Introduction

The "Deployment and Infrastructure" component of this project focuses on providing a streamlined and scalable way to host and serve the Moondream server, which is a crucial component for the Magnitude testing framework. The provided source files outline the steps to deploy the Moondream server on the Modal cloud platform, leveraging its containerization and auto-scaling capabilities. This allows developers to easily set up and manage the Moondream server, ensuring it is available and responsive for running Magnitude tests.

## Modal Setup

The first step in deploying the Moondream server is to set up the Modal platform. This involves creating a Modal account, installing the Modal Python package, and authenticating with the Modal CLI. The `infra/README.md` file provides a clear set of instructions for this initial setup process.

```md
## Set up Modal
1. Create an account at [modal.com](https://modal.com)
2. Run `pip install modal` to install the modal Python package
3. Run `modal setup` to authenticate (if this doesn't work, try `python -m modal setup`)
> More info: https://modal.com/docs/guide
```

Sources: [infra/README.md:4-8]()

## Deploying Moondream

Once the Modal setup is complete, the `moondream.py` script can be used to deploy the Moondream server on the Modal platform. This script automates the process of downloading the Moondream server and model weights, storing them in a Modal volume, and deploying the server as a web endpoint.

```python
@app.function(
    image=image,
    gpu="A10G",
    volumes={"/moondream": volume},
    timeout=1800,
    scaledown_window=300, # 5 minutes
    #min_containers=1 # uncomment to keep a container warm 24/7 (expensive!)
)
@modal.web_server(port=2020, startup_timeout=600, label="moondream")
def server():
    # ...
```

Sources: [infra/moondream.py:32-39]()

The deployment script allows for customization of various options, such as the GPU configuration, container scaling behavior, and minimum number of containers to keep running.

```md
Common options you may want to change:
- `gpu`: GPU configuration. See Modal's [pricing page](https://modal.com/pricing) for details on different available GPUs and their cost. Also see [comparison](#gpu-comparison) below.
- `scaledown_window`: Time in seconds a container will wait to shut down after receiving no requests. If higher, will let you run tests after longer without needing the container to cold-start again.
- `min_containers`: By setting this option you force Modal to keep some number of containers open to handle requests. This means Modal will also bill you for those containers all the time, but eliminates cold-starts.
```

Sources: [infra/README.md:22-28]()

The `README.md` file also provides a helpful comparison of different GPU configurations available on Modal, along with their approximate inference speed and cost per hour.

```md
| GPU         | Approximate inference speed per Moondream action | Modal cost per hour |
| ----------- | ------------------------------------------------ | ------------------- |
| H100        | ~200ms                                           | $3.95               |
| A100 (40GB) | ~300ms                                           | $2.10               |
| A10G        | ~500ms                                           | $1.10               |
| T4          | ~800ms                                           | $0.59               |
```

Sources: [infra/README.md:32-37]()

## Moondream Server Download and Setup

The `moondream.py` script handles the download and setup of the Moondream server binary. It first checks if the server binary is already present in the Modal volume. If not, it downloads the binary from a specified URL and stores it in the volume.

```python
if not os.path.exists(SERVER_DEST_PATH):
    try:
        with requests.get(SERVER_DOWNLOAD_URL, stream=True) as r:
            r.raise_for_status()
            with open(SERVER_DEST_PATH, 'wb') as f:
                for chunk in r.iter_content(chunk_size=8192):
                    f.write(chunk)
        # Commit the downloaded file explicitly to the persistent volume
        volume.commit()
    except requests.exceptions.RequestException as e:
        # Minimal error handling for download failure
        raise RuntimeError(f"Failed to download server: {e}")
```

Sources: [infra/moondream.py:18-28]()

After downloading the server binary, the script sets the necessary execute permissions and starts the server process.

```python
# Set execute permissions, raise error automatically if it fails
subprocess.run(["chmod", "+x", SERVER_DEST_PATH], check=True)

server_args = [SERVER_DEST_PATH]
subprocess.Popen(server_args)
```

Sources: [infra/moondream.py:30-33]()

## Hugging Face Cache

The deployment script also sets up a cache directory for Hugging Face, which is likely used by the Moondream server for model weights and other resources.

```python
HF_CACHE_DIR = "/moondream/cache"
# ...
os.environ['HF_HOME'] = HF_CACHE_DIR
os.makedirs(HF_CACHE_DIR, exist_ok=True)
```

Sources: [infra/moondream.py:7, 13-14]()

## Conclusion

The "Deployment and Infrastructure" component of this project provides a streamlined and scalable way to host and serve the Moondream server on the Modal cloud platform. The provided source files outline the steps for setting up the Modal environment, deploying the Moondream server as a web endpoint, and customizing various deployment options such as GPU configuration and container scaling behavior. Additionally, the scripts handle the download and setup of the Moondream server binary and the Hugging Face cache directory. This infrastructure setup ensures that the Moondream server is readily available and responsive for running Magnitude tests, enabling developers to focus on writing and executing tests without worrying about the underlying server deployment and management.