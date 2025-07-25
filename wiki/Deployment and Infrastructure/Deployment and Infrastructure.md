<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [infra/README.md](https://github.com/aanickode/magnitude/blob/main/infra/README.md)
- [docs/customizing/self-hosting-moondream.mdx](https://github.com/aanickode/magnitude/blob/main/docs/customizing/self-hosting-moondream.mdx)

</details>

# Deployment and Infrastructure

## Introduction

Moondream is a powerful AI model that can be used for various tasks, including automating web interactions. This wiki page focuses on the deployment and infrastructure aspects of Moondream within the context of the Magnitude project. It covers different options for hosting and running the Moondream server, including local deployment, self-hosting on a Virtual Private Cloud (VPC), and deploying on the Modal cloud platform.

## Local Deployment

Running Moondream locally on the same machine where Magnitude tests are executed can be beneficial for local development, especially on high-end machines. To set up local deployment, follow these steps:

1. Download the appropriate Moondream server executable from the official website (https://moondream.ai/c/moondream-server).
2. Run the downloaded executable, following the provided instructions.
3. Configure the `magnitude.config.ts` file with the local base URL, typically `http://localhost:2020/v1`.

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

It's important to note that local inference on a CPU can be slow, and a newer macOS machine or a GPU is recommended for better performance. If local deployment is not feasible, consider deploying Moondream on Modal or self-hosting it on a VPC.

Sources: [docs/customizing/self-hosting-moondream.mdx:8-21]()

## Self-hosting on a VPC

If your company has strict requirements regarding infrastructure hosting, you may need to self-host Moondream inside your company's Virtual Private Cloud (VPC) on your preferred cloud provider. The specific setup will depend on the cloud provider, but generally, the following requirements must be met:

1. A compute instance within your VPC with a GPU.
2. A Linux distribution with CUDA configured on the compute instance.
3. Run the Moondream server executable on this instance.
4. Ensure that the instance is accessible via port 2020 to your CI runners or development machines.
5. Configure the test runners to connect to the appropriate URL within your VPC.

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

Sources: [docs/customizing/self-hosting-moondream.mdx:28-41]()

## Deploying on Modal

One of the easiest ways to deploy Moondream is by hosting it on the Modal cloud platform. The Magnitude project provides a deployment script (`moondream.py`) to automate this process.

### Setting up Modal

1. Create an account at [modal.com](https://modal.com).
2. Install the Modal Python package by running `pip install modal`.
3. Authenticate with Modal by running `modal setup` (or `python -m modal setup` if the previous command doesn't work).

Modal provides $30 in free credits per month.

Sources: [infra/README.md:3-8](), [docs/customizing/self-hosting-moondream.mdx:49-53]()

### Deploying Moondream

1. Clone the Magnitude repository: `git clone https://github.com/magnitudedev/magnitude.git`.
2. Navigate to the `infra` directory: `cd magnitude/infra`.
3. Deploy the Moondream server using the provided script: `modal deploy moondream.py`.

This script will automatically download the Moondream server and model weights to a Modal volume and deploy the endpoint. The Moondream endpoint will be available at `https://<your-modal-username>--moondream.modal.run/v1`.

Sources: [infra/README.md:11-16](), [docs/customizing/self-hosting-moondream.mdx:57-62]()

### Configuring Magnitude

After deploying Moondream on Modal, you can configure the `magnitude.config.ts` file with the Modal base URL to run tests powered by the deployed Moondream server:

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

Note that there may be some cold start times since Modal automatically scales down containers when not in use.

Sources: [infra/README.md:19-28](), [docs/customizing/self-hosting-moondream.mdx:64-74]()

### Customizing the Deployment

You can customize the Modal deployment by modifying the `moondream.py` deployment script according to your needs. Some common options you may want to change include:

- `gpu`: GPU configuration. Refer to Modal's [pricing page](https://modal.com/pricing) for details on different available GPUs and their costs.
- `scaledown_window`: Time in seconds a container will wait to shut down after receiving no requests. Increasing this value will allow you to run tests for longer without needing the container to cold-start again.
- `min_containers`: By setting this option, you can force Modal to keep a certain number of containers open to handle requests. This eliminates cold starts but also means you'll be billed for those containers all the time.

Sources: [docs/customizing/self-hosting-moondream.mdx:77-84]()

#### GPU Comparison

Here's a breakdown of how quickly different GPUs available on Modal can handle typical requests made by Magnitude to Moondream:

| GPU         | Approximate inference time per action | Modal cost per hour |
| ----------- | -------------------------------------- | ------------------- |
| H100        | ~200ms                                 | $3.95               |
| A100 (40GB) | ~300ms                                 | $2.10               |
| A10G        | ~500ms                                 | $1.10               |
| T4          | ~800ms                                 | $0.59               |

Since Magnitude needs to wait for the page to stabilize, a GPU like the A10G may provide a good balance between performance and cost, but any of these options can work well.

Sources: [docs/customizing/self-hosting-moondream.mdx:87-96]()

## Conclusion

Deploying and running the Moondream server is a crucial aspect of the Magnitude project. This wiki page covered three main options: local deployment, self-hosting on a VPC, and deploying on the Modal cloud platform. Each option has its advantages and considerations, and the choice depends on factors such as development environment, compliance requirements, and performance needs. The Modal deployment option, in particular, provides a convenient and scalable solution for hosting Moondream, with the ability to customize the deployment based on specific requirements.