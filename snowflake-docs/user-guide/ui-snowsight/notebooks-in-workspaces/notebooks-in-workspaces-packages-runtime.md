---
title: "Managing packages and runtime"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-packages-runtime"
---

# Managing packages and runtime

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

Snowflake Notebooks run inside a pre-built container environment optimized for scalable AI/ML development powered by Snowflake Container Runtime.

## Python versions

Snowflake Notebooks support Python versions from 3.10 to 3.12. When creating a notebook service, select the Python version that best fits your workload requirements.

## Pre-installed Snowflake Container Runtime packages

Snowflake Container Runtime version 2.2 includes approximately 100 packages and libraries that support a wide range of ML development tasks inside Snowflake.

The following sections list a curated subset of pre-installed packages (40 entries per environment) available for each Python version of Snowflake Container Runtime version `2.2`.

Note

To view the full list of pre-installed packages for your current notebook environment, run `pip freeze` in a Python cell or in the notebook terminal.

### CPU version 2.2

The following packages are available for each Python version of CPU version `2.2`:

Python 3.10Python 3.11Python 3.12

CPU Container Runtime Python 3.10 version `2.2` includes the following packages:

| Package | Version |
| --- | --- |
| absl-py | 2.3.1 |
| aiobotocore | 2.26.0 |
| aiohappyeyeballs | 2.6.1 |
| aiohttp | 3.13.3 |
| aiohttp-cors | 0.8.1 |
| aioitertools | 0.13.0 |
| aiosignal | 1.4.0 |
| altair | 5.5.0 |
| annotated-doc | 0.0.4 |
| annotated-types | 0.7.0 |
| anyio | 4.12.1 |
| appdirs | 1.4.4 |
| argon2-cffi | 25.1.0 |
| argon2-cffi-bindings | 25.1.0 |
| arrow | 1.4.0 |
| arviz | 0.23.1 |
| asn1crypto | 1.5.1 |
| asttokens | 3.0.1 |
| async-lru | 2.1.0 |
| async-timeout | 5.0.1 |
| attrs | 25.4.0 |
| babel | 2.17.0 |
| bayesian-optimization | 1.5.1 |
| beautifulsoup4 | 4.14.3 |
| bleach | 6.3.0 |
| blinker | 1.9.0 |
| boto3 | 1.41.5 |
| botocore | 1.41.5 |
| cachetools | 5.5.2 |
| CausalPy | 0.5.0 |
| certifi | 2026.1.4 |
| cffi | 1.17.1 |
| charset-normalizer | 3.4.4 |
| click | 8.2.1 |
| clikit | 0.6.2 |
| cloudpickle | 3.1.1 |
| cmdstanpy | 1.3.0 |
| colorama | 0.4.6 |
| colorful | 0.5.8 |
| comm | 0.2.3 |

See moreShow less

Expand

CPU Container Runtime Python 3.11 version `2.2` includes the following packages:

| Package | Version |
| --- | --- |
| absl-py | 2.3.1 |
| aiobotocore | 2.26.0 |
| aiohappyeyeballs | 2.6.1 |
| aiohttp | 3.13.3 |
| aiohttp-cors | 0.8.1 |
| aioitertools | 0.13.0 |
| aiosignal | 1.4.0 |
| altair | 5.5.0 |
| annotated-doc | 0.0.4 |
| annotated-types | 0.7.0 |
| anyio | 4.12.1 |
| appdirs | 1.4.4 |
| argon2-cffi | 25.1.0 |
| argon2-cffi-bindings | 25.1.0 |
| arrow | 1.4.0 |
| arviz | 0.23.1 |
| asn1crypto | 1.5.1 |
| asttokens | 3.0.1 |
| async-lru | 2.1.0 |
| attrs | 25.4.0 |
| babel | 2.17.0 |
| bayesian-optimization | 1.5.1 |
| beautifulsoup4 | 4.14.3 |
| better\_optimize | 0.2.0 |
| bleach | 6.3.0 |
| blinker | 1.9.0 |
| boto3 | 1.41.5 |
| botocore | 1.41.5 |
| cachetools | 5.5.2 |
| CausalPy | 0.7.0 |
| certifi | 2026.1.4 |
| cffi | 1.17.1 |
| charset-normalizer | 3.4.4 |
| click | 8.2.1 |
| clikit | 0.6.2 |
| cloudpickle | 3.1.1 |
| cmdstanpy | 1.3.0 |
| colorama | 0.4.6 |
| colorful | 0.5.8 |
| comm | 0.2.3 |

See moreShow less

Expand

CPU Container Runtime Python 3.12 version `2.2` includes the following packages:

| Package | Version |
| --- | --- |
| absl-py | 2.3.1 |
| accelerate | 1.12.0 |
| aiobotocore | 2.26.0 |
| aiohappyeyeballs | 2.6.1 |
| aiohttp | 3.13.3 |
| aiohttp-cors | 0.8.1 |
| aioitertools | 0.13.0 |
| aiosignal | 1.4.0 |
| altair | 5.5.0 |
| annotated-doc | 0.0.4 |
| annotated-types | 0.7.0 |
| anyio | 4.12.1 |
| appdirs | 1.4.4 |
| argon2-cffi | 25.1.0 |
| argon2-cffi-bindings | 25.1.0 |
| arrow | 1.4.0 |
| arviz | 0.23.1 |
| asn1crypto | 1.5.1 |
| asttokens | 3.0.1 |
| async-lru | 2.1.0 |
| attrs | 25.4.0 |
| babel | 2.17.0 |
| bayesian-optimization | 1.5.1 |
| beautifulsoup4 | 4.14.3 |
| better\_optimize | 0.2.0 |
| bleach | 6.3.0 |
| blinker | 1.9.0 |
| boto3 | 1.41.5 |
| botocore | 1.41.5 |
| cachetools | 5.5.2 |
| CausalPy | 0.7.0 |
| certifi | 2026.1.4 |
| cffi | 1.17.1 |
| charset-normalizer | 3.4.4 |
| click | 8.2.1 |
| clikit | 0.6.2 |
| cloudpickle | 3.1.1 |
| cmdstanpy | 1.3.0 |
| colorama | 0.4.6 |
| colorful | 0.5.8 |

See moreShow less

Expand

### GPU version 2.2

The following packages are available for each Python version of GPU version `2.2`:

Python 3.10Python 3.11Python 3.12

GPU Container Runtime Python 3.10 version `2.2` includes the following packages:

| Package | Version |
| --- | --- |
| absl-py | 2.3.1 |
| accelerate | 1.12.0 |
| aiobotocore | 2.26.0 |
| aiohappyeyeballs | 2.6.1 |
| aiohttp | 3.13.3 |
| aiohttp-cors | 0.8.1 |
| aioitertools | 0.13.0 |
| aiosignal | 1.4.0 |
| airportsdata | 20250909 |
| altair | 5.5.0 |
| annotated-doc | 0.0.4 |
| annotated-types | 0.7.0 |
| anyio | 4.12.1 |
| appdirs | 1.4.4 |
| argon2-cffi | 25.1.0 |
| argon2-cffi-bindings | 25.1.0 |
| arrow | 1.4.0 |
| arviz | 0.23.1 |
| asn1crypto | 1.5.1 |
| astor | 0.8.1 |
| asttokens | 3.0.1 |
| async-lru | 2.1.0 |
| async-timeout | 5.0.1 |
| attrs | 25.4.0 |
| babel | 2.17.0 |
| bayesian-optimization | 1.5.1 |
| beautifulsoup4 | 4.14.3 |
| blake3 | 1.0.8 |
| bleach | 6.3.0 |
| blinker | 1.9.0 |
| boto3 | 1.41.5 |
| botocore | 1.41.5 |
| cachetools | 5.5.2 |
| CausalPy | 0.5.0 |
| certifi | 2026.1.4 |
| cffi | 1.17.1 |
| charset-normalizer | 3.4.4 |
| click | 8.2.1 |
| clikit | 0.6.2 |
| cloudpickle | 3.1.1 |

See moreShow less

Expand

GPU Container Runtime Python 3.11 version `2.2` includes the following packages:

| Package | Version |
| --- | --- |
| absl-py | 2.3.1 |
| accelerate | 1.12.0 |
| aiobotocore | 2.26.0 |
| aiohappyeyeballs | 2.6.1 |
| aiohttp | 3.13.3 |
| aiohttp-cors | 0.8.1 |
| aioitertools | 0.13.0 |
| aiosignal | 1.4.0 |
| airportsdata | 20250909 |
| altair | 5.5.0 |
| annotated-doc | 0.0.4 |
| annotated-types | 0.7.0 |
| anyio | 4.12.1 |
| appdirs | 1.4.4 |
| argon2-cffi | 25.1.0 |
| argon2-cffi-bindings | 25.1.0 |
| arrow | 1.4.0 |
| arviz | 0.23.1 |
| asn1crypto | 1.5.1 |
| astor | 0.8.1 |
| asttokens | 3.0.1 |
| async-lru | 2.1.0 |
| attrs | 25.4.0 |
| babel | 2.17.0 |
| bayesian-optimization | 1.5.1 |
| beautifulsoup4 | 4.14.3 |
| better\_optimize | 0.2.0 |
| blake3 | 1.0.8 |
| bleach | 6.3.0 |
| blinker | 1.9.0 |
| boto3 | 1.41.5 |
| botocore | 1.41.5 |
| cachetools | 5.5.2 |
| CausalPy | 0.7.0 |
| certifi | 2026.1.4 |
| cffi | 1.17.1 |
| charset-normalizer | 3.4.4 |
| click | 8.2.1 |
| clikit | 0.6.2 |
| cloudpickle | 3.1.1 |

See moreShow less

Expand

GPU Container Runtime Python 3.12 version `2.2` includes the following packages:

| Package | Version |
| --- | --- |
| absl-py | 2.3.1 |
| accelerate | 1.12.0 |
| aiobotocore | 2.26.0 |
| aiohappyeyeballs | 2.6.1 |
| aiohttp | 3.13.3 |
| aiohttp-cors | 0.8.1 |
| aioitertools | 0.13.0 |
| aiosignal | 1.4.0 |
| airportsdata | 20250909 |
| altair | 5.5.0 |
| annotated-doc | 0.0.4 |
| annotated-types | 0.7.0 |
| anyio | 4.12.1 |
| appdirs | 1.4.4 |
| argon2-cffi | 25.1.0 |
| argon2-cffi-bindings | 25.1.0 |
| arrow | 1.4.0 |
| arviz | 0.23.1 |
| asn1crypto | 1.5.1 |
| astor | 0.8.1 |
| asttokens | 3.0.1 |
| async-lru | 2.1.0 |
| attrs | 25.4.0 |
| babel | 2.17.0 |
| bayesian-optimization | 1.5.1 |
| beautifulsoup4 | 4.14.3 |
| better\_optimize | 0.2.0 |
| blake3 | 1.0.8 |
| bleach | 6.3.0 |
| blinker | 1.9.0 |
| boto3 | 1.41.5 |
| botocore | 1.41.5 |
| cachetools | 5.5.2 |
| CausalPy | 0.7.0 |
| certifi | 2026.1.4 |
| cffi | 1.17.1 |
| charset-normalizer | 3.4.4 |
| click | 8.2.1 |
| clikit | 0.6.2 |
| cloudpickle | 3.1.1 |

See moreShow less

Expand

## Installing additional packages

Snowflake supports package installation from several sources.

### From external repositories

After configuring External Access Integrations (EAIs) for secure repository access, you can install packages directly from external sources such as
PyPI. Users have access to a comprehensive ecosystem of packages beyond the pre-installed runtime, ensuring secure connectivity to external repos.

You can run `pip install` in a Python cell or in the notebook terminal.

For more information, see [Set up external access for Snowflake Notebooks](../notebooks-external-access).

### From `requirements.txt`

You can specify and install required package versions in a `requirements.txt` file to ensure a consistent environment setup. Install them
using the following command:

CopyExpand

```
!pip install -r requirements.txt
```

Show lessSee more

Scroll to top

Note

If the package version specified in `requirements.txt` conflicts with supported versions of the
[pre-installed packages](../../../developer-guide/snowflake-ml/container-runtime-ml.html#label-container-runtime-for-ml-packages), the Python environment may break. Validate compatibility before
installing.

### From Workspace files

You can download or build `.whl` or `.py` files, upload them to your workspace, and install or import them.

* **Wheel files (.whl):** Upload the `.whl` file and install it:

  CopyExpand

  ```
  !pip install file_name.whl
  ```

  Show lessSee more

  Scroll to top

  If the package contains dependencies that are not already installed, upload the complete dependency tree (either directly into Workspaces or to a
  stage). Alternatively, attach an EAI that allows access to a repository where the package can be downloaded (for example, PyPI).
* **Python files (.py):** Modules stored in your workspace can be imported directly for sharing utilities and functions across notebooks.
  For example:

  CopyExpand

  ```
  from my_utils import my_func
  ```

  Show lessSee more

  Scroll to top

### From a Snowflake stage

Stages provide secure and governed package deployment by leveraging existing Snowflake data storage and governance controls for package files. Use
the Snowpark session to retrieve package files from a Snowflake stage into the container environment for import and use. For example:

CopyExpand

```
from snowflake.snowpark.context import get_active_session
import sys

session = get_active_session()
session.file.get("@db.schema.stage_name/math_tools.py", "/tmp")

sys.path.append("/tmp")
import math_tools

math_tools.add_one(3)
```

Show lessSee more

Scroll to top

## Runtime management

### Runtime pinning

All notebook services are pinned to the Runtime selected at creation unless you explicitly change it by editing the service. For example, a notebook
service created on `Runtime 2.0` will not be automatically upgraded when new Runtime versions are released.

### Runtime vulnerability scanning

Snowflake scans the Runtime images daily for security vulnerabilities. High or critical Common Vulnerabilities and Exposures (CVEs) are addressed by
releasing new Runtime versions within 30 days of detection.

Existing notebook services can continue using Runtimes with detected CVEs. However, Runtimes with known CVEs cannot be selected when creating new
notebook services.
