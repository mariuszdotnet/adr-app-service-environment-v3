# Python Module Management

## Prologue (Summary)

The following ADR reviews the use of [Oryx](https://github.com/microsoft/Oryx/tree/main) on Azure App Service for Linux.  Oryx is a build system which automatically compiles source code repos into runnable artifacts. It is used to build web apps for Azure App Service and other platforms.  The discussion here applies to the [Python runtime](https://learn.microsoft.com/en-us/azure/app-service/configure-language-python) on both multitenant Azure App Service as well as to the dedicated/isolated App Service Environment v3 platform options.

## Discussion (Context)

Enterprises have a need to control [Software Supply Chain](https://devblogs.microsoft.com/engineering-at-microsoft/the-journey-to-secure-the-software-supply-chain-at-microsoft/) risks that come with package distribution from public repositories.  In this scenario we are focusing on the **Python Libraries** which are which are imported by [pip](https://github.com/pypa/pip) at runtime from [PyPi](https://pypi.org/) public index server by default.

For full context it is recommended to review the following documentation:

- [How Python Builds Works in App Service](https://learn.microsoft.com/en-us/azure/app-service/configure-language-python#customize-startup-command)
- [PIP Requirements File Format](https://pip.pypa.io/en/stable/reference/requirements-file-format/)
- [Installing packages from Other indexes](https://packaging.python.org/en/latest/tutorials/installing-packages/)
- [PIP configuration](https://pip.pypa.io/en/stable/topics/configuration/)

## Solution (Decision)

  1. The easiest option is to [Configure a custom container for Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/configure-custom-container?tabs=debian&pivots=container-linux).  This allows you to build in all the dependencies with you CI/CD process.  The Oryx build is not used.
  2. The next option is to use the different options that are available for specifying requirements in the **requirements.txt** file.

  ```python
  # It is possible to refer to a library and and URLs.
  urllib3 @ https://github.com/urllib3/urllib3/archive/refs/tags/1.26.8.zip

  # It is possible to refer to specific local distribution paths.
  ./downloads/numpy-1.9.2-cp34-none-win32.whl

  # It is possible to refer to URLs.
  http://wxpython.org/Phoenix/snapshot-builds/wxPython_Phoenix-3.0.3.dev1820+49a8884-cp34-none-win_amd64.whl
  ```
  
  3. The next option is to overwrite the [pip configuration](https://pip.pypa.io/en/stable/topics/configuration/).  There are various level of this as per the documentation.  In the context of ORYX it would be best to define the **PIP_CONFIG_FILE** environment variable to the file location as it overwrites any other configurations.  This can be done in a few different ways.

  ```bash
  PIP_CONFIG_FILE=pip.conf
  ```
**Note:** When using App Service with a Virtual Network or an App Service Environment there are [network dependencies](https://github.com/microsoft/Oryx/blob/main/doc/hosts/appservice.md#network-dependencies). You will need to allow outbound access from the webapp to **oryx-cdn.microsoft.io** on port **443**. **oryx-cdn.microsoft.io** hosts the Oryx packages corresponding to each SDK language and version. If this network dependency is blocked, then App Service will not be able to build your application using Oryx.

## Consequences (Results)

  1. With the **custom container** option the user takes on the responsible for managing updates to container at all levels.

  2. With the **requirements.txt** file options there is no central enforcement point.

  3. The **pip configuration** allow for a central enforcement point.  An example of the [config file](https://pip.pypa.io/en/stable/topics/configuration/) is as follows:

  ```python
  [global]
  timeout = 60
  index-url = https://download.zope.org/ppix
  ```

## Observations (Suggestions)

- The **pip configuration** option is most desirable as it allows for a central enforcement point.  It is also agnostic of the hosting platform and specific to **Python** development tooling.
