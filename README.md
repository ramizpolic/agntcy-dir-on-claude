# AGNTCY Directory on Claude

> **AGNTCY Directory: From Zero to Building With Agents You Can Find and Trust**
> **https://www.youtube.com/watch?v=-ux8hTpcr3c**

This guide walks through the full lifecycle of agentic development using [AGNTCY Directory](https://github.com/agntcy/dir) on Claude. It shows how to install the necessary skills into your agentic environment, run a local directory service, author and publish agent records, and finally, discover, verify, and use agents - both locally and across the network.

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

2. Reload skills so Claude picks up the new skill:

   ```
   /reload-skills
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

4. Activate the installed agent. What to run depends on what the record carried:

   - If it is an **Agent Skill** (`core/language_model/agentskills`), reload skills:

     ```
     /reload-skills
     ```

   - If it is an **MCP server** (`integration/mcp`), restart the session. MCP servers
     connect at startup, so a skills reload will not pick them up. Then confirm it
     connected:

     ```
     /mcp
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

4. Activate the installed agent, as in step 3.4:

   - If it is an **Agent Skill** (`core/language_model/agentskills`), reload skills:

     ```
     /reload-skills
     ```

   - If it is an **MCP server** (`integration/mcp`), restart the session, then run
     `/mcp` to confirm it connected.

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

Your node republishes its announcements every 36 hours by default, which is far too slow for a live walkthrough — peers that join during the demo would not converge on your records in time. Shorten the interval so newly joined nodes pick them up within a minute:

```
export DIRECTORY_DAEMON_SERVER_ROUTING_REPUBLISH_INTERVAL="1m"
```

## About data sharing

Records are shared between nodes via sync: after discovering records on the network, the sync pulls the data via OCI protocol from remote peers into your own OCI registry. By default, your DIR runs a local registry not exposed to the network. You can configure your DIR to use a publicly accessible OCI registry such as GHCR to share your records with others.

There is no registry to create up front — GHCR creates the package the first time your node pushes to it. What you need is a token.

1. Create a **classic** personal access token at https://github.com/settings/tokens/new with the `write:packages`, `read:packages`, and `delete:packages` scopes. Fine-grained tokens scope package access through repositories and will not work for a package that is not linked to one.

2. Put it in your environment without leaving it in your shell history, then confirm it works:

   ```sh
   read -rs GHCR_TOKEN && export GHCR_TOKEN
   echo "$GHCR_TOKEN" | docker login ghcr.io -u <user> --password-stdin
   ```

3. Save the following as `daemon.config.yaml`, replacing `<user>` and `<repo>`. `<repo>` is a package name of your choosing, not an existing repository. Copy it whole — `--config` is read as-is and is not merged with the built-in defaults, so a partial file would drop everything it omits:

   ```yaml
   server:
     listen_address: "localhost:8888"
     oasf_api_validation:
       schema_url: "https://schema.oasf.outshift.com"
     store:
       provider: "oci"
       oci:
         local_dir: "" # empty: store records in the registry below, not on disk
         registry_address: "ghcr.io"
         repository_name: "<user>/<repo>"
         auth_config:
           insecure: false
       verification:
         enabled: true
     routing:
       listen_address: "/ip4/0.0.0.0/tcp/8999"
       key_path: "node.key"
       datastore_dir: "routing"
       bootstrap_peers:
         - "/dns4/routing.ads.outshift.io/tcp/5555/p2p/12D3KooWLf9p3cedc86xGQBaqak6rAFmQk1HxKAK1yh7umHE3amu"
       republish_interval: "1m"
       directory_oci_address: "https://ghcr.io/<user>/<repo>"
       gossipsub:
         enabled: true
       autosync:
         enabled: false
       relay_service: false
       force_reachability_private: false
       force_reachability_public: false
     database:
       type: "sqlite"
       sqlite:
         path: "dir.db"
     publication:
       scheduler_interval: 5m
       worker_count: 1
       worker_timeout: 30m
     http_gateway:
       enabled: true
       listen_address: ":8889"
       public_url: "http://localhost:8889"
     naming:
       ttl: 168h

   reconciler:
     regsync:
       enabled: true
       interval: 1s
     metrics:
       enabled: true
       interval: 1m
     indexer:
       enabled: true
       interval: 1s
     signature:
       enabled: true
       interval: 1s
       ttl: 168h
       record_timeout: 30s
     name:
       enabled: true
       interval: 1m
       ttl: 168h
       record_timeout: 30s
     scan:
       enabled: true
       interval: 1s
       ttl: 168h
       record_timeout: 2m
       mcp_cli_path: "mcp-scanner"
       skill_cli_path: "skill-scanner"
       a2a_cli_path: "a2a-scanner"
     local_registry:
       registry_address: "ghcr.io"
       repository_name: "<user>/<repo>"
       auth_config:
         insecure: false
         # Leave both empty. Before v1.6.2 they exist only so the matching
         # environment variables are resolved; a key absent from the file is
         # ignored. From v1.6.2 they can be dropped entirely.
         username: ""
         password: ""
     database:
       type: "sqlite"
       sqlite:
         path: "dir.db"

   runtime:
     enabled: false
   ```

4. Pass the credentials through the environment, never the file:

   ```sh
   export DIRECTORY_DAEMON_SERVER_STORE_OCI_AUTH_CONFIG_USERNAME="<user>"
   export DIRECTORY_DAEMON_SERVER_STORE_OCI_AUTH_CONFIG_PASSWORD="$GHCR_TOKEN"
   export DIRECTORY_DAEMON_RECONCILER_LOCAL_REGISTRY_AUTH_CONFIG_USERNAME="<user>"
   export DIRECTORY_DAEMON_RECONCILER_LOCAL_REGISTRY_AUTH_CONFIG_PASSWORD="$GHCR_TOKEN"
   ```

5. Start the daemon with the config:

   ```sh
   dirctl daemon start --config ./daemon.config.yaml
   ```

6. After the first record is pushed, make the package public at
   `https://github.com/users/<user>/packages/container/<repo>/settings`. Packages default to private, and peers pull from it anonymously — while it stays private, network search will show your records but sync will fail for everyone else.

> [!CAUTION]
> **DO NOT** store your credentials in the config file. The `username` and `password` keys above must stay empty and are only there so the environment variables resolve. A token written into `daemon.config.yaml` is trivially committed by accident, and it grants write access to every package you own. Keep it in the shell session or a secret manager.

> [!NOTE]
> The config file is what makes this work on every release. Up to and including v1.6.1 the daemon ignores `DIRECTORY_DAEMON_SERVER_ROUTING_DIRECTORY_OCI_ADDRESS`, `..._ROUTING_REPUBLISH_INTERVAL`, and the two `..._RECONCILER_LOCAL_REGISTRY_AUTH_CONFIG_*` variables, so those settings have to come from the file. From v1.6.2 all four are honoured from the environment, and you can drive the whole setup with the env vars in the earlier sections instead.

> Check these demo records stored on GHCR at
> 
> https://github.com/users/ramizpolic/packages/container/package/agntcy-dir-on-claude
