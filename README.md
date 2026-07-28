# AGNTCY Directory on Claude

> **AGNTCY Directory: From Zero to Building With Agents You Can Find and Trust**
> **https://www.youtube.com/watch?v=-ux8hTpcr3c**

This guide walks through the full lifecycle of agentic development using [AGNTCY Directory](https://github.com/agntcy/dir) on Claude. It shows how to install the neccessary skills into your agentic environment, run a local directory service, author and publish agent records, and finally, discover, verify, and use agents - both locally and across the network.

> [!NOTE]
> For issues and questions around the setup and workflow, please file an issue on https://github.com/agntcy/dir

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed.

Start Claude Code with the context and examples:

```sh
git clone https://github.com/ramizpolic/agntcy-dir-on-claude
cd agntcy-dir-on-claude
claude
```

## Steps to reproduce

Type each prompt below into Claude and let the agent finish before moving on.

### 1. Getting started

1. Install the skill:

   ```
   Install the agent skill from the .skill/ folder in https://github.com/agntcy/dir
   into the .claude/skills/ folder of my current project.
   ```

2. Reload plugins so Claude picks up the new skill:

   ```
   /reload-plugins
   ```

3. Start a local AGNTCY Directory:

   ```
   Set up a local AGNTCY Directory in the background and verify the
   environment is healthy.
   ```

### 2. Authoring records

1. Inspect the example OASF records shipped with the demo:

   ```
   Summarize the OASF records from ./records
   ```

2. Publish them (validate, push, sign, publish):

   ```
   Push them to my local directory, sign them using OIDC, publish them
   to the network without waiting, and report the summary.
   ```

### 3. Local discovery

1. Search the local directory for a matching agent:

   ```
   Find agents locally that can draft release notes from git commit history.
   Show the results as a table with trust status.
   ```

2. Check safety and signature of the best match:

   ```
   Is the best match record safe, and does its signature verify?
   ```

3. Install the discovered agent:

   ```
   Install the best matching agent into my current Claude project.
   ```

4. Reload plugins:

   ```
   /reload-plugins
   ```

5. Use the newly installed changelog agent:

   ```
   Using the changelog agent, give me a high-level changelog summary for the
   last month in https://github.com/agntcy/dir
   ```

### 4. Distributed discovery

1. Search the network and sync the results locally:

   ```
   Find agents on the network only that can help me with UNIX command usage and
   best practices and synchronize them to my local directory. 
   Show the results as a table with trust status once the sync is completed.
   ```
   

2. Check safety and signature of the best match:

   ```
   Is the best match record safe, and does its signature verify?
   ```

3. Install the discovered agent:

   ```
   Install the best matching agent into my current Claude project.
   ```

4. Reload plugins:

   ```
   /reload-plugins
   ```

5. Use the newly installed UNIX agent:

   ```
   Using the UNIX agent, tell me what is the best way to check if
   something exists in a file using cat and grep.
   ```

### 5. Wrap up

```
Stop my local AGNTCY Directory.
```

## About security scanning

In order to enable security scanning capabilities shown in the demo, the following scanners must be installed on your system and available on your `PATH`:
- [A2A Scanner](https://github.com/cisco-ai-defense/a2a-scanner)
- [MCP Scanner](https://github.com/cisco-ai-defense/mcp-scanner)
- [Skills scanner](https://github.com/cisco-ai-defense/skill-scanner)

## About distributed discovery

Discovery in AGNTCY Directory works at two levels: local search against your own node, and network-wide search across peers. The local node joins the AGNTCY network via a routing bootstrap peer, letting it announce published records and discover records announced by others. Network search matches on taxonomy names and returns which peers hold which records. This discovery result can then be used to sync the records from remote sources to your local node for use. By default, your DIR is not part of any network. You can configure your DIR to join public AGNTCY network bootstrap peer by configuring the service with:

```
export DIRECTORY_DAEMON_SERVER_ROUTING_BOOTSTRAP_PEERS="/dns4/routing.ads.outshift.io/tcp/5555/p2p/12D3KooWLf9p3cedc86xGQBaqak6rAFmQk1HxKAK1yh7umHE3amu" 
```

## About data sharing

Records are shared between nodes via sync: after discovering records on the network, the sync pulls the data via OCI protocol from remote peers into your own OCI registry. By default, your DIR runs a local registry not exposed to the network. You can configure your DIR to use a publically accessible OCI registry such as GHCR to share your records with others. You can do this by configuring the service with:

```
## Set GHCR as storage backend for the API and workers
export DIRECTORY_DAEMON_SERVER_STORE_OCI_LOCAL_DIR=""
export DIRECTORY_DAEMON_SERVER_STORE_OCI_REGISTRY_ADDRESS="ghcr.io"
export DIRECTORY_DAEMON_SERVER_STORE_OCI_REPOSITORY_NAME="<user>/<repo>"
export DIRECTORY_DAEMON_SERVER_STORE_OCI_AUTH_CONFIG_INSECURE=false
export DIRECTORY_DAEMON_SERVER_STORE_OCI_AUTH_CONFIG_USERNAME=<user>
export DIRECTORY_DAEMON_SERVER_STORE_OCI_AUTH_CONFIG_PASSWORD=<token>

export DIRECTORY_DAEMON_RECONCILER_LOCAL_REGISTRY_REGISTRY_ADDRESS="ghcr.io"
export DIRECTORY_DAEMON_RECONCILER_LOCAL_REGISTRY_REPOSITORY_NAME="<user>/<repo>"
export DIRECTORY_DAEMON_RECONCILER_LOCAL_REGISTRY_AUTH_CONFIG_INSECURE=false
export DIRECTORY_DAEMON_RECONCILER_LOCAL_REGISTRY_AUTH_CONFIG_USERNAME=<user>
export DIRECTORY_DAEMON_RECONCILER_LOCAL_REGISTRY_AUTH_CONFIG_PASSWORD=<token>

## Set GHCR for discovery in routing
export DIRECTORY_DAEMON_SERVER_ROUTING_DIRECTORY_OCI_ADDRESS="https://ghcr.io/<user>/<repo>"
```

> Check these demo records stored on GHCR at
> 
> https://github.com/users/ramizpolic/packages/container/package/agntcy-dir-on-claude
