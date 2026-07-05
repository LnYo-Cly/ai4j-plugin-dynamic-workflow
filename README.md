# ai4j-plugin-dynamic-workflow

AI4J extension plugin that exposes a `workflow` tool and `workflow` command for host-mediated dynamic workflow orchestration.

The plugin intentionally does **not** execute JavaScript, create subagents, or create git worktrees. It returns a stable JSON envelope so the host can parse, approve, schedule, or reject the workflow request.

## Install

```xml
<dependency>
  <groupId>io.github.lnyo-cly</groupId>
  <artifactId>ai4j-plugin-dynamic-workflow</artifactId>
  <version>0.1.0-SNAPSHOT</version>
</dependency>
```

It depends on `io.github.lnyo-cly:ai4j-extension-api:2.4.0`.
Until that API artifact is available from your configured Maven repository, install it from the AI4J SDK checkout first:

```bash
git clone https://github.com/LnYo-Cly/ai4j ../ai4j-sdk
mvn -f ../ai4j-sdk/pom.xml -Droot.publish.skip=false -pl ai4j-extension-api -am -DskipTests install
```

## Enable

```java
ExtensionRegistry registry = ExtensionRegistry.discover()
        .enable("dynamic-workflow")
        .exposeTool("workflow");
```

Strict resource activation:

```java
ExtensionRegistry registry = ExtensionRegistry.discover()
        .enable("dynamic-workflow")
        .requireExplicitResourceActivation()
        .allowCommand("workflow")
        .allowSkill("dynamic-workflow-orchestration")
        .allowPrompt("dynamic-workflow-script")
        .exposeTool("workflow");
```

## Tool result

`workflow` returns:

```json
{
  "type": "ai4j.dynamic_workflow.request",
  "source": "tool",
  "tool": "workflow",
  "status": "pending_host_workflow_execution",
  "hostAction": "execute_dynamic_workflow",
  "scriptRuntime": "host_mediated",
  "blocking": "host_decides",
  "argumentsRaw": "{...}"
}
```

`argumentsRaw` is capped at 64 KiB and adds `argumentsTruncated: true` when capped.

## Validate

```bash
mvn -DskipTests=false test
```

## Pi reference choice

This plugin follows the small core contract from `Michaelliv/pi-dynamic-workflows`: one `workflow` tool, literal `export const meta`, and deterministic script guidance. The larger `QuintinShaw/pi-dynamic-workflows` features (background manager, resume journal, model tiers, worktree isolation) are host/runtime concerns for later AI4J agent/coding integration.
