# Altair data server

**This repo is no longer maintained.**
In Altair 5,
the recommended way to work with large datasets is via Vegafusion.
The documentation contains a section of [how to use the Vegafusion renderer in Altair](https://altair-viz.github.io/user_guide/large_datasets.html#vegafusion-data-transformer)
and if you need additional performance you can [use the Vegafusion widget renderer](https://vegafusion.io/widget_renderer.html) instead. 

---

[![build status](http://img.shields.io/travis/altair-viz/altair_data_server/master.svg?style=flat)](https://travis-ci.org/altair-viz/altair_data_server)
[![github actions](https://github.com/altair-viz/altair_data_server/workflows/build/badge.svg)](https://github.com/altair-viz/altair_data_server/actions?query=workflow%3Abuild)
[![github actions](https://github.com/altair-viz/altair_data_server/workflows/lint/badge.svg)](https://github.com/altair-viz/altair_data_server/actions?query=workflow%3Alint)
[![code style black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/altair-viz/altair_data_server/master?urlpath=lab/tree/AltairDataServer.ipynb)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/altair-viz/altair_data_server/blob/master/AltairDataServer.ipynb)


This is a data transformer plugin for [Altair](http://altair-viz.github.io)
that transparently serves data for Altair charts via a background WSGI server.

Note that charts will only render as long as your Python session is active.

The data server is a good option when you'll be **generating multiple charts as
part of an exploration of data**.

## Usage

First install the package and its dependencies:

```
$ pip install altair_data_server
```

Next import altair and enable the data server:

```python
import altair as alt
alt.data_transformers.enable('data_server')
```
Now when you create an Altair chart, the data will be served in the background
rather than embedded in the chart specification.

Once you are finished with exploration and want to generate charts that
will have their data fully embedded in the notebook, you can restore the
default data transformer:

```python
alt.data_transformers.enable('default')
```

and carry on from there.

## Remote Systems
Remotely-hosted notebooks (like JupyterHub or Binder) usually do not allow the end
user to access arbitrary ports. To enable users to work on that setup, make sure
[jupyter-server-proxy](https://github.com/jupyterhub/jupyter-server-proxy) is
installed on the jupyter server, and use the proxied data server transformer:

```python
alt.data_transformers.enable('data_server_proxied')
```

The `urlpath` parameter allows you to override the prefix of the proxy URL. By
default, it's set to `..`, which is currently the only way to make it work for
arbitrary users when running inside the classic notebook on Binder. If you
intend your notebooks to be run on Binder but inside JupyterLab, change it to
`.` instead, which will work provided JupyterLab is in the [default
workspace](https://jupyterlab.readthedocs.io/en/stable/user/urls.html#managing-workspaces-ui).

```python
# for notebooks intended for JupyterLab on Binder
alt.data_transformers.enable('data_server_proxied', urlpath='.')
```

On a custom JupyterHub instance, a much more robust option is to take advantage
of JupyterHub's [`/user-redirect`](https://jupyterhub.readthedocs.io/en/stable/reference/urls.html#user-redirect)
feature (which is not available on Binder):

```python
# this will work for any JupyterHub user, whether they're using the classic
# notebook, JupyterLab in the default workspace, or JupyterLab in a named
# workspace
alt.data_transformers.enable('data_server_proxied', urlpath='/user-redirect')
```

If your JupyterHub lives somewhere else than at your server's root, add the
appropriate prefix to `urlpath`.

## Example

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/altair-viz/altair_data_server/master?urlpath=lab/tree/AltairDataServer.ipynb)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/altair-viz/altair_data_server/blob/master/AltairDataServer.ipynb)

You can see this in action, as well as read some of the motivation for this
plugin, in the example notebook: [AltairDataServer.ipynb](AltairDataServer.ipynb).
Click the Binder or Colab links above to try it out in your browser.

## Known Issues

Because [jupyter-server-proxy](https://github.com/jupyterhub/jupyter-server-proxy)
requires at least Python 3.5, the methods described in
[Remote Systems](#remote-systems) do not work do not work for older versions of Python.
