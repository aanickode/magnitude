<details>
<summary>Relevant source files</summary>

The following file was used as context for generating this wiki page:

- [docs/customizing/self-hosting-moondream.mdx](https://github.com/aanickode/magnitude/blob/main/docs/customizing/self-hosting-moondream.mdx)
</details>

# Deployment & Infrastructure

## Introduction

This page covers the deployment and infrastructure options for running the Moondream server, which is a crucial component of the Magnitude testing framework. Moondream is an AI-powered engine that enables Magnitude to understand and interact with web applications during testing. The deployment options range from running Moondream locally on the same machine as the tests, to self-hosting it on a private infrastructure or cloud platform with GPU acceleration. The choice of deployment method depends on factors such as development needs, compliance requirements, and performance considerations.

## Local Deployment

Running Moondream locally on the same machine as the Magnitude tests is a convenient option for local development, especially on high-end machines. To set up local deployment, follow these steps:

1. Download the appropriate Moondream server executable for your operating system (macOS or Linux) from the [official website](https://moondream.ai/c/moondream-server).
2. Run the downloaded executable following the provided instructions.
3. Configure the `magnitude.config.ts` file with the local base URL, as shown in the example:

```ts
import { type MagnitudeConfig } from 'magnitude-test';

export default {
    url: "http://localhost:5173",
    executor: {
        provider: 'moondream',
        options: {
            baseUrl: 'http://localhost:2020/v1'
        }
    }
} satisfies MagnitudeConfig;
```

With this configuration, Magnitude will run tests using the locally running Moondream server.

It's important to note that local inference on a CPU can be slow, especially on older machines. For better performance, it's recommended to use a newer macOS machine or a machine with a GPU. Alternatively, you can consider [self-hosting Moondream on Modal](#self-hosting-moondream-on-modal), which provides GPU acceleration.

Sources: [docs/customizing/self-hosting-moondream.mdx:11-32]()

## Self-Hosting on a VPC

If your company has strict requirements regarding infrastructure hosting, you may need to self-host Moondream within your company's Virtual Private Cloud (VPC) on your preferred cloud provider. The specific setup will depend on the cloud provider, but generally, the following requirements must be met:

1. A compute instance within your VPC with a GPU.
2. A Linux distribution with CUDA configured on the compute instance.
3. Running the [Moondream server](https://moondream.ai/c/moondream-server) executable on this instance.
4. Ensuring that the instance is accessible via port 2020 to your CI runners or development machines.
5. Configuring the test runners to connect to the appropriate URL, as shown in the example:

```ts
import { type MagnitudeConfig } from 'magnitude-test';

export default {
    url: "http://localhost:5173",
    executor: {
        provider: 'moondream',
        options: {
            baseUrl: 'https://some-host-in-my-vpc/v1'
        }
    }
} satisfies MagnitudeConfig;
```

If you are an enterprise trying to configure Magnitude in your company, you can reach out to the Magnitude team directly at founders@magnitude.run for assistance.

Sources: [docs/customizing/self-hosting-moondream.mdx:35-51]()

## Self-Hosting Moondream on Modal

One of the easiest ways to deploy Moondream is by hosting it on the Modal cloud platform, which provides GPU acceleration. Magnitude provides a [deployment script](https://github.com/magnitudedev/magnitude/blob/main/infra/moondream.py) to facilitate this process.

### Setting up Modal

1. Create an account at [modal.com](https://modal.com).
2. Install the Modal Python package by running `pip install modal`.
3. Authenticate with Modal by running `modal setup` (or `python -m modal setup` if the previous command doesn't work).

For more information, refer to the [Modal documentation](https://modal.com/docs/guide).

### Deploying Moondream

1. Clone the Magnitude repository: `git clone https://github.com/magnitudedev/magnitude.git`.
2. Navigate to the `infra` directory: `cd magnitude/infra`.
3. Deploy the Moondream server by running `modal deploy moondream.py`.

This script will automatically download the Moondream server and model weights to a Modal volume and deploy the endpoint. Your Moondream endpoint will be available at `https://<your-modal-username>--moondream.modal.run/v1`.

4. Configure the `magnitude.config.ts` file with the Modal base URL:

```ts
import { type MagnitudeConfig } from 'magnitude-test';

export default {
    url: "http://localhost:5173",
    executor: {
        provider: 'moondream',
        options: {
            baseUrl: 'https://<your-modal-username>--moondream.modal.run/v1'
        }
    }
} satisfies MagnitudeConfig;
```

Now you can start running tests powered by your newly deployed Moondream server on Modal.

It's important to note that there may be some cold start times since Modal automatically scales down containers when not in use. Additionally, the Modal server will be publicly accessible by default. The Magnitude team is working on a better way to make it configurable with a custom API key.

Sources: [docs/customizing/self-hosting-moondream.mdx:54-86]()

### Customizing the Modal Deployment

You can customize the Modal deployment by modifying the `moondream.py` deployment script to fit your needs. Some common options you may want to change include:

- `gpu`: GPU configuration. See Modal's [pricing page](https://modal.com/pricing) for details on different available GPUs and their cost. Refer to the [GPU Comparison](#gpu-comparison) section for more information.
- `scaledown_window`: Time in seconds a container will wait to shut down after receiving no requests. If higher, it will allow you to run tests after longer periods without needing the container to cold-start again.
- `min_containers`: By setting this option, you force Modal to keep a specified number of containers open to handle requests. This means Modal will also bill you for those containers all the time, but it eliminates cold-starts.

Sources: [docs/customizing/self-hosting-moondream.mdx:89-96]()

### GPU Comparison

When deploying Moondream on Modal, the choice of GPU can significantly impact performance and cost. Here's a breakdown of how quickly different GPUs available on Modal can handle typical requests made by Magnitude to Moondream:

| GPU         | Approximate inference time per action | Modal cost per hour |
| ----------- | ------------------------------------------------ | ------------------- |
| H100        | ~200ms                                           | $3.95               |
| A100 (40GB) | ~300ms                                           | $2.10               |
| A10G        | ~500ms                                           | $1.10               |
| T4          | ~800ms                                           | $0.59               |

Since Magnitude needs to wait for the page to stabilize after each action, a GPU like the A10G may provide a good balance between performance and cost. However, any of these GPUs can work well for running Moondream with Magnitude.

Sources: [docs/customizing/self-hosting-moondream.mdx:99-107]()

## Mermaid Diagrams

### Local Deployment Flow

```mermaid
flowchart TD
    A[Developer Machine] -->|1. Download Moondream Server| B[Moondream Server Executable]
    B -->|2. Run Executable| C[Moondream Server Running Locally]
    A -->|3. Configure magnitude.config.ts| D[Magnitude Tests]
    D -->|4. Send Requests| C
    C -->|5. Respond with Inferences| D
```

This diagram illustrates the flow for running Moondream locally on the same machine as the Magnitude tests. The developer first downloads the Moondream server executable and runs it locally. Then, the `magnitude.config.ts` file is configured to point to the locally running Moondream server. During test execution, Magnitude sends requests to the local Moondream server, which responds with the necessary inferences.

Sources: [docs/customizing/self-hosting-moondream.mdx:11-32]()

### Self-Hosting on a VPC

```mermaid
flowchart TD
    A[Developer Machine] -->|1. Configure magnitude.config.ts| B[Magnitude Tests]
    C[VPC Instance] -->|2. Run Moondream Server| D[Moondream Server]
    B -->|3. Send Requests| D
    D -->|4. Respond with Inferences| B
```

When self-hosting Moondream on a Virtual Private Cloud (VPC), the developer configures the `magnitude.config.ts` file to point to the Moondream server running on the VPC instance. The Magnitude tests then send requests to the Moondream server hosted on the VPC, which responds with the necessary inferences.

Sources: [docs/customizing/self-hosting-moondream.mdx:35-51]()

### Self-Hosting on Modal

```mermaid
sequenceDiagram
    participant Developer
    participant ModalCLI
    participant ModalPlatform
    participant MagnitudeTests

    Developer->>ModalCLI: Run modal deploy moondream.py
    ModalCLI->>ModalPlatform: Deploy Moondream server
    ModalPlatform-->>ModalCLI: Moondream endpoint URL
    ModalCLI-->>Developer: Moondream endpoint URL

    Developer->>MagnitudeTests: Configure magnitude.config.ts with Modal endpoint
    MagnitudeTests->>ModalPlatform: Send requests to Moondream endpoint
    ModalPlatform-->>MagnitudeTests: Respond with inferences
```

This sequence diagram illustrates the process of self-hosting Moondream on the Modal platform. The developer first runs the `modal deploy moondream.py` script, which deploys the Moondream server on the Modal platform. The Modal CLI provides the Moondream endpoint URL, which the developer then configures in the `magnitude.config.ts` file. During test execution, Magnitude sends requests to the Moondream endpoint on Modal, which responds with the necessary inferences.

Sources: [docs/customizing/self-hosting-moondream.mdx:54-86]()

## Tables

### Deployment Options Summary

| Option                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                