<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>WebMCP Model Card Specification</title>
  <script src="https://www.w3.org/Tools/respec/respec-w3c" class="remove" defer></script>
  <script class="remove">
    var respecConfig = {
      specStatus: "CG-DRAFT",
      group: "aikr",
      shortName: "webmcp-model-card",
      editors: [
        {
          name: "Paola Di Maio",
          url: "https://ronin-institute.org",
          company: "Ronin Institute",
          companyURL: "https://ronin-institute.org",
          w3cid: null
        },
        {
          name: "Claude",
          company: "Anthropic",
          companyURL: "https://anthropic.com"
        }
      ],
      latestVersion: "https://starborn.github.io/webmcp/webmcp-model-card-spec.html",
      edDraftURI: "https://starborn.github.io/webmcp/webmcp-model-card-spec.html",
      github: {
        repoURL: "https://github.com/Starborn/webmcp",
        branch: "main"
      },
      logos: [],
      otherLinks: [
        {
          key: "Related Specifications",
          data: [
            {
              value: "MCP Model Card Specification v1.0",
              href: "https://claude.ai/chat/8b25ad92-1093-448a-9de6-3197e06316d5"
            },
            {
              value: "WebMCP API Specification (W3C CG Draft)",
              href: "https://webmachinelearning.github.io/webmcp/"
            },
            {
              value: "Model Context Protocol (MCP)",
              href: "https://modelcontextprotocol.io/specification/latest"
            }
          ]
        }
      ],
      xref: "web-platform"
    };
  </script>
  <style>
    table.comparison {
      border-collapse: collapse;
      width: 100%;
      margin: 1em 0;
    }
    table.comparison th,
    table.comparison td {
      border: 1px solid #ccc;
      padding: 0.5em 0.75em;
      text-align: left;
      vertical-align: top;
    }
    table.comparison th {
      background-color: #f0f0f0;
    }
    table.comparison tr.highlight td {
      background-color: #fff3cd;
    }
    .draft-warning {
      background-color: #fff3cd;
      border: 2px solid #ffc107;
      padding: 1em 1.5em;
      margin: 1.5em 0;
      border-radius: 4px;
    }
    .draft-warning strong {
      color: #856404;
    }
    .new-field {
      color: #155724;
      font-weight: bold;
    }
    .modified-field {
      color: #856404;
      font-weight: bold;
    }
    code {
      background-color: #f5f5f5;
      padding: 0.1em 0.3em;
      border-radius: 3px;
    }
  </style>
</head>
<body>

  <section id="abstract">
    <p>
      This specification defines a standardized <strong>Model Card</strong> format for documenting
      WebMCP tool implementations. WebMCP Model Cards provide structured metadata about
      browser-side tool registrations exposed through the <code>navigator.modelContext</code> API,
      enabling transparency, trust, and interoperability in the browser-based AI agent ecosystem.
    </p>
    <p>
      This DRAFT  specification aims to  address the distinct architectural properties of
      WebMCP: client-side execution, browser security boundaries, session-dependent authentication,
      declarative and imperative API modes, and human-in-the-loop interaction patterns.
    </p>
    <div class="draft-warning">
      <strong>DRAFT PROPOSAL FOR DISCUSSION</strong> -- This document is an early draft intended
      to stimulate discussion within the W3C AI Knowledge Representation Community Group and the
      W3C Web Machine Learning Community Group. It has not been reviewed or endorsed by either
      group. All sections are subject to revision. Feedback is welcome via the
      <a href="https://github.com/Starborn/webmcp/issues">GitHub issue tracker</a>.
    </div>
  </section>

  <section id="sotd">
    <p>
      This is a Draft Community Group Report produced by the
      <a href="https://www.w3.org/community/aikr/">W3C AI Knowledge Representation Community Group</a>
      in coordination with the
      <a href="https://www.w3.org/community/webmachinelearning/">W3C Web Machine Learning Community Group</a>.
      It is not a W3C Standard nor is it on the W3C Standards Track.
    </p>
    <p>
      This specification is a <strong>DRAFT proposal for discussion</strong>. It has not undergone
      formal review by either community group. The authors invite comment on all aspects of this
      document, particularly on the fields that are new relative to the MCP Model Card Specification
      v1.0 (marked <span class="new-field">[NEW]</span> throughout) and on fields whose semantics
      have been modified for the browser context (marked <span class="modified-field">[MODIFIED]</span>).
    </p>
  </section>

  <!-- ============================================================ -->
  <!-- SECTION 1: INTRODUCTION                                      -->
  <!-- ============================================================ -->
  <section id="introduction">
    <h2>Introduction</h2>

    <section id="purpose">
      <h3>Purpose</h3>
      <p>
        The WebMCP API [[webmcp]] enables web applications to expose JavaScript-based tools
        to AI agents through the browser's <code>navigator.modelContext</code> interface.
        These tools can be registered declaratively (via HTML form attributes) or imperatively
        (via JavaScript calls to <code>navigator.modelContext.registerTool()</code>).
      </p>
      <p>
        As with backend MCP servers, there is currently no standardized way to document a
        WebMCP tool's capabilities, security characteristics, operational constraints, or
        intended use cases. This specification addresses that gap for the browser context.
      </p>
    </section>

    <section id="scope">
      <h3>Scope</h3>
      <p>This specification covers:</p>
      <ul>
        <li>Metadata schema for documenting WebMCP tool implementations</li>
        <li>Required and optional fields specific to browser-side tool registration</li>
        <li>Validation criteria adapted for client-side execution environments</li>
        <li>JSON Schema definition</li>
        <li>Mapping to and differences from the MCP Model Card Specification v1.0</li>
      </ul>
    </section>

    <section id="relationship-to-mcp-model-card">
      <h3>Relationship to MCP Model Card Specification v1.0</h3>
      <p>
        The <a href="https://claude.ai/chat/8b25ad92-1093-448a-9de6-3197e06316d5">MCP Model Card
        Specification v1.0</a> (Di Maio and Claude, January 2026) defines a documentation format
        for backend MCP servers that expose tools, resources, and prompts to Large Language Models
        through the Model Context Protocol [[MCP]]. That specification assumes a server-side
        execution model with JSON-RPC transport, hosted infrastructure, and explicit API key or
        OAuth-based authentication.
      </p>
      <p>
        WebMCP differs from MCP in several architecturally significant ways that necessitate
        a distinct -- though compatible -- model card format:
      </p>
      <table class="comparison">
        <thead>
          <tr>
            <th>Dimension</th>
            <th>MCP (Backend)</th>
            <th>WebMCP (Browser)</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Execution environment</td>
            <td>Server-side process</td>
            <td>Browser JavaScript runtime (same-origin)</td>
          </tr>
          <tr>
            <td>Transport</td>
            <td>JSON-RPC over stdio, SSE, or HTTP</td>
            <td>In-process JavaScript callback; browser mediates agent communication</td>
          </tr>
          <tr>
            <td>Authentication</td>
            <td>API keys, OAuth 2.1, custom headers</td>
            <td>Inherits browser session (cookies, tokens); no separate auth layer</td>
          </tr>
          <tr>
            <td>Tool registration</td>
            <td>Server manifest at connection time</td>
            <td>Declarative (HTML form attributes) or Imperative (<code>registerTool()</code>)</td>
          </tr>
          <tr>
            <td>Lifecycle</td>
            <td>Persistent server process</td>
            <td>Page-scoped; tools exist only while the page is loaded</td>
          </tr>
          <tr>
            <td>User interaction</td>
            <td>Not specified by protocol</td>
            <td>Built-in via <code>requestUserInteraction()</code></td>
          </tr>
          <tr>
            <td>Security boundary</td>
            <td>Network perimeter, API gateway</td>
            <td>Browser sandbox, same-origin policy, CSP</td>
          </tr>
          <tr>
            <td>Agent relationship</td>
            <td>Agent is MCP client connecting to MCP server</td>
            <td>Browser mediates; web page is a "model context provider"</td>
          </tr>
          <tr>
            <td>Protocol compliance</td>
            <td>Implements MCP JSON-RPC specification</td>
            <td>Shares MCP's tool primitive; does NOT implement JSON-RPC</td>
          </tr>
          <tr>
            <td>Specification status</td>
            <td>Agentic AI Foundation (Linux Foundation) stewardship</td>
            <td>W3C Web Machine Learning Community Group Draft</td>
          </tr>
        </tbody>
      </table>
      <p>
        Because of these differences, several MCP Model Card v1.0 fields do not apply to WebMCP
        (for example, <code>supported_transports</code>, <code>protocol_version</code>) and several
        new fields are required (for example, <code>api_mode</code>, <code>browser_compatibility</code>,
        <code>session_context</code>). Section 4 of this document provides a complete field-by-field
        comparison.
      </p>
    </section>

    <section id="terminology">
      <h3>Terminology</h3>
      <p>
        This specification uses terminology defined in the WebMCP API specification [[webmcp]]:
      </p>
      <dl>
        <dt><dfn>agent</dfn></dt>
        <dd>An autonomous assistant that can understand a user's goals and take actions on
          the user's behalf to achieve them.</dd>
        <dt><dfn>browser's agent</dfn></dt>
        <dd>An agent provided by or through the browser, built directly into it or hosted
          via an extension or plug-in.</dd>
        <dt><dfn>AI platform</dfn></dt>
        <dd>A provider of agentic assistants such as OpenAI's ChatGPT, Anthropic's Claude,
          or Google's Gemini.</dd>
        <dt><dfn>model context provider</dfn></dt>
        <dd>A web page that exposes tools through the <code>navigator.modelContext</code> API.</dd>
        <dt><dfn>declarative API</dfn></dt>
        <dd>Tool registration via HTML form attributes (<code>tool-name</code>,
          <code>tool-description</code>, <code>tool-param-description</code>).</dd>
        <dt><dfn>imperative API</dfn></dt>
        <dd>Tool registration via JavaScript calls to
          <code>navigator.modelContext.registerTool()</code> or
          <code>navigator.modelContext.provideContext()</code>.</dd>
      </dl>
    </section>
  </section>

  <!-- ============================================================ -->
  <!-- SECTION 2: CONFORMANCE                                       -->
  <!-- ============================================================ -->
  <section id="conformance">
    <p>
      As well as sections marked as non-normative, all authoring guidelines, diagrams,
      examples, and notes in this specification are non-normative. Everything else in
      this specification is normative.
    </p>
    <p>
      The key words MAY, MUST, MUST NOT, OPTIONAL, RECOMMENDED, REQUIRED, SHALL, SHOULD,
      and SHOULD NOT in this document are to be interpreted as described in
      <a href="https://www.rfc-editor.org/rfc/rfc2119">BCP 14</a> [[RFC2119]]
      when, and only when, they appear in all capitals, as shown here.
    </p>
  </section>

  <!-- ============================================================ -->
  <!-- SECTION 3: WEBMCP MODEL CARD SCHEMA                          -->
  <!-- ============================================================ -->
  <section id="schema">
    <h2>WebMCP Model Card Schema</h2>

    <section id="schema-overview">
      <h3>Schema Overview</h3>
      <pre class="example" title="Top-level schema structure">
{
  "webmcp_model_card_version": "1.0.0",
  "tool_identity": { ... },
  "api_mode": { ... },
  "tool_schema": { ... },
  "session_context": { ... },
  "browser_compatibility": { ... },
  "security_properties": { ... },
  "interaction_patterns": { ... },
  "accessibility": { ... },
  "deployment_context": { ... },
  "evaluation_results": { ... }
}
      </pre>
    </section>

    <!-- 3.2 Tool Identity -->
    <section id="tool-identity">
      <h3>Tool Identity</h3>
      <p>
        <span class="modified-field">[MODIFIED from MCP v1.0 <code>server_metadata</code>]</span>
        In MCP Model Card v1.0, this section is called <code>server_metadata</code> and documents
        a backend MCP server process. In WebMCP, the unit of documentation is the individual
        <strong>tool</strong> (or set of tools) registered by a web page, not a server.
      </p>
      <pre class="example" title="Tool Identity fields">
{
  "tool_identity": {
    "name": "string (REQUIRED) -- maps to ModelContextTool.name",
    "version": "string (REQUIRED) -- semver of the tool implementation",
    "description": "string (REQUIRED) -- maps to ModelContextTool.description",
    "author": "string (REQUIRED) -- person or organization",
    "page_url": "URL (REQUIRED) -- the page that registers this tool",
    "domain": "string (REQUIRED) -- the origin serving this tool",
    "repository": "URL (OPTIONAL) -- source code repository",
    "license": "string (REQUIRED) -- SPDX identifier",
    "attribution": "string (OPTIONAL) -- human-authored | ai-co-created | generated",
    "last_updated": "ISO 8601 date (REQUIRED)"
  }
}
      </pre>
      <p class="note">
        MCP Model Card v1.0 includes <code>protocol_version</code> and
        <code>supported_transports</code> in server metadata. These fields are not applicable
        to WebMCP because WebMCP does not implement the MCP JSON-RPC protocol and has no
        configurable transport layer. The browser mediates all agent communication.
      </p>
    </section>

    <!-- 3.3 API Mode -->
    <section id="api-mode">
      <h3>API Mode</h3>
      <p>
        <span class="new-field">[NEW -- no equivalent in MCP Model Card v1.0]</span>
        WebMCP supports two distinct registration mechanisms with different properties.
        This section documents which mode the tool uses and why it matters.
      </p>
      <pre class="example" title="API Mode fields">
{
  "api_mode": {
    "mode": "string (REQUIRED) -- 'declarative' | 'imperative' | 'both'",
    "declarative_form_id": "string (OPTIONAL) -- HTML form element ID if declarative",
    "registration_method": "string (REQUIRED if imperative) --
        'registerTool' | 'provideContext'",
    "dynamic_registration": "boolean (REQUIRED) -- whether tools are
        registered/unregistered at runtime based on page state",
    "rationale": "string (OPTIONAL) -- why this mode was chosen"
  }
}
      </pre>
      <p>
        The distinction matters because declarative tools are statically analyzable -- an
        agent or auditing tool can inspect the HTML source to discover tool capabilities
        without executing JavaScript. Imperative tools can have arbitrary runtime behavior,
        including conditional registration based on user state, dynamic schema generation,
        and state-dependent execution logic.
      </p>
    </section>

    <!-- 3.4 Tool Schema -->
    <section id="tool-schema">
      <h3>Tool Schema</h3>
      <p>
        <span class="modified-field">[MODIFIED from MCP v1.0 <code>tool_documentation</code>]</span>
        Adapted from the MCP Model Card's <code>tool_documentation</code> section but
        scoped to a single tool (since a WebMCP page may register multiple independent
        tools, each with its own model card) and aligned with the
        <code>ModelContextTool</code> dictionary defined in [[webmcp]].
      </p>
      <pre class="example" title="Tool Schema fields">
{
  "tool_schema": {
    "input_schema": "object (REQUIRED) -- JSON Schema per ModelContextTool.inputSchema",
    "output_format": "string (REQUIRED) -- description of the return value structure",
    "output_schema": "object (OPTIONAL) -- JSON Schema for the return value",
    "error_states": [
      {
        "condition": "string (REQUIRED)",
        "behavior": "string (REQUIRED) -- throw | return error object | silent fail",
        "message": "string (OPTIONAL)"
      }
    ],
    "annotations": {
      "readOnlyHint": "boolean (OPTIONAL) -- maps to ToolAnnotations.readOnlyHint"
    },
    "examples": [
      {
        "description": "string (REQUIRED)",
        "input": "object (REQUIRED)",
        "expected_output": "any (OPTIONAL)"
      }
    ]
  }
}
      </pre>
      <p class="note">
        MCP Model Card v1.0 documents multiple tools per card (since an MCP server typically
        exposes several tools). In WebMCP, the RECOMMENDED practice is one model card per
        tool, though a single card MAY document a cohesive set of tools registered by the
        same page if they share a common purpose and security profile.
      </p>
    </section>

    <!-- 3.5 Session Context -->
    <section id="session-context">
      <h3>Session and Authentication Context</h3>
      <p>
        <span class="new-field">[NEW -- no equivalent in MCP Model Card v1.0]</span>
        WebMCP tools inherit the browser session. This section documents the authentication
        and session dependencies that are implicit in the execution environment.
      </p>
      <pre class="example" title="Session Context fields">
{
  "session_context": {
    "requires_authentication": "boolean (REQUIRED)",
    "authentication_mechanism": "string (REQUIRED if authenticated) --
        'cookie' | 'token' | 'oauth_session' | 'other'",
    "session_persistence": "string (REQUIRED) -- 'page_load' | 'tab_session' | 'persistent'",
    "required_roles": ["string (OPTIONAL) -- roles or permissions needed"],
    "anonymous_access": "boolean (REQUIRED) -- whether tool works for unauthenticated visitors",
    "cross_tab_isolation": "string (REQUIRED) --
        'isolated' | 'shared_session' | 'not_specified'",
    "session_expiry_behavior": "string (OPTIONAL) --
        what happens when the session expires mid-execution"
  }
}
      </pre>
      <p>
        In backend MCP, authentication is explicit: API keys, OAuth tokens, or custom headers
        are documented in the MCP Model Card v1.0 <code>security_profile</code>. In WebMCP,
        authentication is implicit -- the tool executes in the context of whatever browser
        session the user has established. This makes session documentation critical for agents
        that need to understand whether a tool will work in a given context.
      </p>
    </section>

    <!-- 3.6 Browser Compatibility -->
    <section id="browser-compatibility">
      <h3>Browser Compatibility</h3>
      <p>
        <span class="new-field">[NEW -- no equivalent in MCP Model Card v1.0]</span>
        Backend MCP servers are browser-independent. WebMCP tools execute in the browser
        and are subject to browser-specific API availability.
      </p>
      <pre class="example" title="Browser Compatibility fields">
{
  "browser_compatibility": {
    "native_support": [
      {
        "browser": "string (REQUIRED) -- e.g. 'Chrome'",
        "minimum_version": "string (REQUIRED) -- e.g. '146'",
        "flag_required": "boolean (REQUIRED)",
        "flag_name": "string (OPTIONAL) -- e.g. 'enable-webmcp-testing'"
      }
    ],
    "polyfill": {
      "available": "boolean (REQUIRED)",
      "package": "string (OPTIONAL) -- e.g. '@mcp-b/global'",
      "documentation": "URL (OPTIONAL)"
    },
    "graceful_degradation": "string (REQUIRED) --
        what happens when WebMCP is not available in the browser",
    "csp_compatibility": "string (OPTIONAL) --
        Content Security Policy requirements or conflicts"
  }
}
      </pre>
    </section>

    <!-- 3.7 Security Properties -->
    <section id="security-properties">
      <h3>Security Properties</h3>
      <p>
        <span class="modified-field">[MODIFIED from MCP v1.0 <code>security_profile</code>]</span>
        The MCP Model Card v1.0 <code>security_profile</code> addresses network-level security
        (TLS, API key rotation, rate limiting at the server). WebMCP security operates within
        the browser sandbox and raises distinct concerns.
      </p>
      <pre class="example" title="Security Properties fields">
{
  "security_properties": {
    "origin_scope": "string (REQUIRED) -- the origin(s) from which this tool can be invoked",
    "csp_requirements": "string (OPTIONAL) -- CSP directives this tool requires or conflicts with",
    "human_in_the_loop": {
      "required": "boolean (REQUIRED)",
      "confirmation_flow": "string (REQUIRED if true) --
          description of the user confirmation mechanism",
      "uses_requestUserInteraction": "boolean (REQUIRED)"
    },
    "data_exposure": {
      "user_data_accessed": ["string (REQUIRED) -- types of user data the tool reads"],
      "data_sent_externally": "boolean (REQUIRED) -- whether the tool sends data outside the origin",
      "external_endpoints": ["URL (OPTIONAL) -- if data is sent externally, where"]
    },
    "exfiltration_risk": {
      "touches_sensitive_data": "boolean (REQUIRED)",
      "accepts_untrusted_input": "boolean (REQUIRED)",
      "communicates_externally": "boolean (REQUIRED)",
      "risk_assessment": "string (REQUIRED) -- 'low' | 'medium' | 'high' | 'critical'",
      "mitigation": "string (OPTIONAL)"
    },
    "agent_invocation_tracking": {
      "uses_agentInvoked_flag": "boolean (OPTIONAL) --
          whether the tool uses SubmitEvent.agentInvoked for server-side differentiation",
      "audit_logging": "boolean (OPTIONAL)"
    },
    "rate_limiting": "string (OPTIONAL) -- client-side rate limiting mechanism if any"
  }
}
      </pre>
      <p class="note">
        The "deadly triad" or "lethal trifecta" -- a tool that (1) touches sensitive data,
        (2) accepts untrusted input, and (3) communicates externally -- represents the
        highest-risk configuration for a WebMCP tool. The <code>exfiltration_risk</code>
        sub-section makes this assessment explicit.
      </p>
    </section>

    <!-- 3.8 Interaction Patterns -->
    <section id="interaction-patterns">
      <h3>Interaction Patterns</h3>
      <p>
        <span class="new-field">[NEW -- no equivalent in MCP Model Card v1.0]</span>
        WebMCP is designed for cooperative workflows where users and agents share context
        within the same web interface. This section documents how the tool participates
        in that interaction.
      </p>
      <pre class="example" title="Interaction Patterns fields">
{
  "interaction_patterns": {
    "statefulness": "string (REQUIRED) --
        'stateless' | 'stateful_page' | 'stateful_session'",
    "state_dependencies": "string (OPTIONAL) --
        description of page state the tool depends on",
    "user_visibility": "string (REQUIRED) --
        'transparent' | 'background' | 'ui_modifying'",
    "concurrent_execution": "boolean (OPTIONAL) --
        whether this tool can run concurrently with other tools",
    "chaining_support": "string (OPTIONAL) --
        whether this tool's output is designed to be input for other tools",
    "expected_latency": "string (OPTIONAL) --
        'instant' | 'sub-second' | 'seconds' | 'long-running'",
    "failure_behavior": "string (REQUIRED) --
        how the tool communicates failure to the agent"
  }
}
      </pre>
    </section>

    <!-- 3.9 Accessibility -->
    <section id="accessibility-section">
      <h3>Accessibility</h3>
      <p>
        <span class="new-field">[NEW -- no equivalent in MCP Model Card v1.0]</span>
        The WebMCP specification explicitly identifies assistive technologies as consumers
        of WebMCP tools alongside AI agents. This section documents accessibility properties.
      </p>
      <pre class="example" title="Accessibility fields">
{
  "accessibility": {
    "aria_compliance": "string (OPTIONAL) -- WAI-ARIA conformance level",
    "assistive_technology_support": "boolean (REQUIRED) --
        whether the tool has been tested with assistive technologies",
    "keyboard_navigable": "boolean (OPTIONAL) --
        if requestUserInteraction is used, whether the UI is keyboard-accessible",
    "screen_reader_tested": "boolean (OPTIONAL)"
  }
}
      </pre>
    </section>

    <!-- 3.10 Deployment Context -->
    <section id="deployment-context">
      <h3>Deployment Context</h3>
      <p>
        <span class="modified-field">[MODIFIED from MCP v1.0 <code>deployment_context</code>]</span>
        In MCP Model Card v1.0, deployment context covers server hosting, scaling, and
        infrastructure. For WebMCP, deployment is the web page itself.
      </p>
      <pre class="example" title="Deployment Context fields">
{
  "deployment_context": {
    "intended_use_cases": ["string (REQUIRED)"],
    "out_of_scope_uses": ["string (RECOMMENDED)"],
    "target_agents": ["string (OPTIONAL) --
        specific agents or agent types this tool is designed for"],
    "page_type": "string (OPTIONAL) --
        'single_page_app' | 'multi_page' | 'progressive_web_app' | 'static'",
    "framework": "string (OPTIONAL) -- e.g. 'React', 'Vue', 'vanilla JS'",
    "known_limitations": ["string (RECOMMENDED)"],
    "maintenance_status": "string (OPTIONAL) --
        'active' | 'maintained' | 'deprecated' | 'archived'"
  }
}
      </pre>
    </section>

    <!-- 3.11 Evaluation Results -->
    <section id="evaluation-results">
      <h3>Evaluation Results</h3>
      <p>
        <span class="modified-field">[MODIFIED from MCP v1.0 <code>evaluation_results</code>]</span>
        Adapted to include browser-specific testing dimensions.
      </p>
      <pre class="example" title="Evaluation Results fields">
{
  "evaluation_results": {
    "functional_testing": {
      "agents_tested": ["string (OPTIONAL) -- which agents have been tested"],
      "browsers_tested": ["string (OPTIONAL)"],
      "test_suite_url": "URL (OPTIONAL)",
      "tests_passed": "integer (OPTIONAL)",
      "tests_failed": "integer (OPTIONAL)"
    },
    "security_audit": {
      "audit_date": "ISO 8601 date (OPTIONAL)",
      "auditor": "string (OPTIONAL)",
      "findings": ["string (OPTIONAL)"]
    },
    "accessibility_audit": {
      "audit_date": "ISO 8601 date (OPTIONAL)",
      "wcag_level": "string (OPTIONAL) -- 'A' | 'AA' | 'AAA'",
      "findings": ["string (OPTIONAL)"]
    },
    "peer_review": {
      "review_date": "ISO 8601 date (OPTIONAL)",
      "reviewers": ["string (OPTIONAL)"],
      "approval_status": "string (OPTIONAL) -- 'approved' | 'conditional' | 'rejected'"
    }
  }
}
      </pre>
    </section>
  </section>

  <!-- ============================================================ -->
  <!-- SECTION 4: FIELD-BY-FIELD COMPARISON                         -->
  <!-- ============================================================ -->
  <section id="comparison">
    <h2>Field-by-Field Comparison with MCP Model Card v1.0</h2>
    <p>
      The following table provides a complete mapping between MCP Model Card Specification v1.0
      fields and this WebMCP Model Card Specification. Fields are categorized as:
    </p>
    <ul>
      <li><strong>Carried forward</strong> -- the field exists in both specifications with
        compatible semantics</li>
      <li><strong>Modified</strong> -- the field exists in both but with changed semantics
        or structure for the browser context</li>
      <li><strong>Removed</strong> -- the MCP v1.0 field does not apply to WebMCP</li>
      <li><strong>New</strong> -- the field exists only in this specification</li>
    </ul>

    <table class="comparison">
      <thead>
        <tr>
          <th>MCP Model Card v1.0 Field</th>
          <th>WebMCP Model Card Field</th>
          <th>Status</th>
          <th>Notes</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>server_metadata.name</code></td>
          <td><code>tool_identity.name</code></td>
          <td>Modified</td>
          <td>Renamed from server to tool scope</td>
        </tr>
        <tr>
          <td><code>server_metadata.version</code></td>
          <td><code>tool_identity.version</code></td>
          <td>Carried forward</td>
          <td></td>
        </tr>
        <tr>
          <td><code>server_metadata.description</code></td>
          <td><code>tool_identity.description</code></td>
          <td>Carried forward</td>
          <td></td>
        </tr>
        <tr>
          <td><code>server_metadata.protocol_version</code></td>
          <td>--</td>
          <td>Removed</td>
          <td>WebMCP does not implement MCP JSON-RPC</td>
        </tr>
        <tr>
          <td><code>server_metadata.supported_transports</code></td>
          <td>--</td>
          <td>Removed</td>
          <td>No configurable transport; browser mediates</td>
        </tr>
        <tr>
          <td><code>server_metadata.language</code></td>
          <td>--</td>
          <td>Removed</td>
          <td>Always JavaScript in browser context</td>
        </tr>
        <tr>
          <td>--</td>
          <td><code>tool_identity.page_url</code></td>
          <td>New</td>
          <td>The page that hosts the tool</td>
        </tr>
        <tr>
          <td>--</td>
          <td><code>tool_identity.domain</code></td>
          <td>New</td>
          <td>Origin serving the tool</td>
        </tr>
        <tr>
          <td>--</td>
          <td><code>api_mode</code> (entire section)</td>
          <td>New</td>
          <td>Declarative vs imperative registration</td>
        </tr>
        <tr>
          <td><code>tool_documentation</code></td>
          <td><code>tool_schema</code></td>
          <td>Modified</td>
          <td>Scoped to single tool; aligned with ModelContextTool dictionary</td>
        </tr>
        <tr>
          <td>--</td>
          <td><code>session_context</code> (entire section)</td>
          <td>New</td>
          <td>Browser session and authentication dependencies</td>
        </tr>
        <tr>
          <td>--</td>
          <td><code>browser_compatibility</code> (entire section)</td>
          <td>New</td>
          <td>Browser versions, flags, polyfill availability</td>
        </tr>
        <tr>
          <td><code>security_profile</code></td>
          <td><code>security_properties</code></td>
          <td>Modified</td>
          <td>Rewritten for browser sandbox model; adds exfiltration risk, HITL, agent tracking</td>
        </tr>
        <tr>
          <td>--</td>
          <td><code>interaction_patterns</code> (entire section)</td>
          <td>New</td>
          <td>Statefulness, user visibility, cooperative workflow properties</td>
        </tr>
        <tr>
          <td>--</td>
          <td><code>accessibility</code> (entire section)</td>
          <td>New</td>
          <td>WebMCP spec explicitly includes assistive technologies as tool consumers</td>
        </tr>
        <tr>
          <td><code>deployment_context</code></td>
          <td><code>deployment_context</code></td>
          <td>Modified</td>
          <td>Rewritten for web deployment; removes hosting/scaling, adds page type and framework</td>
        </tr>
        <tr>
          <td><code>evaluation_results</code></td>
          <td><code>evaluation_results</code></td>
          <td>Modified</td>
          <td>Adds browser testing, accessibility audit, agent testing dimensions</td>
        </tr>
        <tr>
          <td><code>operational_characteristics</code></td>
          <td>--</td>
          <td>Removed</td>
          <td>Server uptime, scaling, hosting not applicable to client-side tools</td>
        </tr>
      </tbody>
    </table>
  </section>

  <!-- ============================================================ -->
  <!-- SECTION 5: VALIDATION                                        -->
  <!-- ============================================================ -->
  <section id="validation">
    <h2>Validation Requirements</h2>

    <section id="schema-validation">
      <h3>Schema Validation</h3>
      <p>All WebMCP Model Cards MUST:</p>
      <ol>
        <li>Conform to JSON Schema specification [[JSON-SCHEMA]]</li>
        <li>Include all fields marked REQUIRED</li>
        <li>Use valid data types for all fields</li>
        <li>Use the value <code>"1.0.0"</code> for <code>webmcp_model_card_version</code>
          when conforming to this version of the specification</li>
      </ol>
    </section>

    <section id="content-validation">
      <h3>Content Validation</h3>
      <p>Model Card content MUST:</p>
      <ol>
        <li>Be accurate and current as of <code>tool_identity.last_updated</code></li>
        <li>Not contain contradictory information across sections</li>
        <li>Document all known security considerations in <code>security_properties</code></li>
        <li>Correctly report the <code>api_mode</code> used by the implementation</li>
      </ol>
    </section>

    <section id="tool-naming">
      <h3>Tool Naming</h3>
      <p>
        Tool names (<code>tool_identity.name</code>) MUST match the <code>name</code> field
        of the corresponding <code>ModelContextTool</code> as registered with the browser.
        Tool names SHOULD be descriptive of the tool's functionality and SHOULD follow
        a consistent naming convention across tools registered by the same page.
      </p>
    </section>
  </section>

  <!-- ============================================================ -->
  <!-- SECTION 6: EXAMPLE                                           -->
  <!-- ============================================================ -->
  <section id="example" class="informative">
    <h2>Example WebMCP Model Card</h2>
    <p>
      The following is a complete example of a WebMCP Model Card for a hypothetical
      restaurant reservation tool registered via the declarative API.
    </p>
    <pre class="example" title="Complete WebMCP Model Card example">
{
  "webmcp_model_card_version": "1.0.0",

  "tool_identity": {
    "name": "book_table",
    "version": "1.2.0",
    "description": "Reserve a table at the restaurant for a specified party size and date",
    "author": "Example Restaurant Group",
    "page_url": "https://restaurant.example/reservations",
    "domain": "restaurant.example",
    "repository": "https://github.com/example-restaurant/webmcp-tools",
    "license": "MIT",
    "attribution": "human-authored",
    "last_updated": "2026-02-20"
  },

  "api_mode": {
    "mode": "declarative",
    "declarative_form_id": "reservation-form",
    "dynamic_registration": false,
    "rationale": "Static form suitable for declarative registration; no runtime logic needed"
  },

  "tool_schema": {
    "input_schema": {
      "type": "object",
      "properties": {
        "party_size": {
          "type": "integer",
          "minimum": 1,
          "maximum": 20,
          "description": "Number of guests"
        },
        "date": {
          "type": "string",
          "format": "date",
          "description": "Reservation date (YYYY-MM-DD)"
        },
        "time": {
          "type": "string",
          "pattern": "^([01]\\d|2[0-3]):([0-5]\\d)$",
          "description": "Reservation time (HH:MM, 24-hour format)"
        },
        "special_requests": {
          "type": "string",
          "description": "Dietary requirements or seating preferences"
        }
      },
      "required": ["party_size", "date", "time"]
    },
    "output_format": "JSON object with confirmation_id, date, time, party_size",
    "error_states": [
      {
        "condition": "No availability for requested date/time",
        "behavior": "return error object",
        "message": "No tables available for the requested date and time"
      },
      {
        "condition": "Party size exceeds capacity",
        "behavior": "return error object",
        "message": "Maximum party size is 20"
      }
    ],
    "annotations": {
      "readOnlyHint": false
    },
    "examples": [
      {
        "description": "Book a table for 4 people",
        "input": {
          "party_size": 4,
          "date": "2026-03-15",
          "time": "19:30"
        },
        "expected_output": {
          "confirmation_id": "RES-2026-0315-001",
          "date": "2026-03-15",
          "time": "19:30",
          "party_size": 4,
          "status": "confirmed"
        }
      }
    ]
  },

  "session_context": {
    "requires_authentication": false,
    "session_persistence": "page_load",
    "anonymous_access": true,
    "cross_tab_isolation": "isolated"
  },

  "browser_compatibility": {
    "native_support": [
      {
        "browser": "Chrome",
        "minimum_version": "146",
        "flag_required": true,
        "flag_name": "enable-webmcp-testing"
      }
    ],
    "polyfill": {
      "available": true,
      "package": "@mcp-b/global",
      "documentation": "https://docs.mcp-b.ai"
    },
    "graceful_degradation": "Falls back to standard HTML form submission"
  },

  "security_properties": {
    "origin_scope": "https://restaurant.example",
    "human_in_the_loop": {
      "required": true,
      "confirmation_flow": "Browser displays reservation summary before submission",
      "uses_requestUserInteraction": true
    },
    "data_exposure": {
      "user_data_accessed": ["form input only"],
      "data_sent_externally": false
    },
    "exfiltration_risk": {
      "touches_sensitive_data": false,
      "accepts_untrusted_input": true,
      "communicates_externally": false,
      "risk_assessment": "low"
    }
  },

  "interaction_patterns": {
    "statefulness": "stateless",
    "user_visibility": "transparent",
    "concurrent_execution": true,
    "expected_latency": "sub-second",
    "failure_behavior": "Returns structured error object with message"
  },

  "accessibility": {
    "assistive_technology_support": true,
    "keyboard_navigable": true,
    "screen_reader_tested": true
  },

  "deployment_context": {
    "intended_use_cases": [
      "AI agent makes restaurant reservation on behalf of user",
      "Browser assistant fills reservation form"
    ],
    "out_of_scope_uses": [
      "Bulk automated reservations",
      "Headless agent reservations without user presence"
    ],
    "page_type": "multi_page",
    "framework": "vanilla JS",
    "maintenance_status": "active"
  },

  "evaluation_results": {
    "functional_testing": {
      "agents_tested": ["Chrome built-in agent (Canary 146)"],
      "browsers_tested": ["Chrome 146 Canary"],
      "tests_passed": 12,
      "tests_failed": 0
    }
  }
}
    </pre>
  </section>

  <!-- ============================================================ -->
  <!-- SECTION 7: OPEN QUESTIONS                                    -->
  <!-- ============================================================ -->
  <section id="open-questions" class="informative">
    <h2>Open Questions for Discussion</h2>
    <p>
      The following questions are unresolved and the editors invite community input:
    </p>
    <ol>
      <li>
        <strong>Granularity:</strong> Should WebMCP Model Cards always document individual tools,
        or should there be a "page-level" card format that documents all tools registered by a
        single page as a coherent set?
      </li>
      <li>
        <strong>Discovery:</strong> How should agents discover the existence of a WebMCP Model
        Card for a given tool? Should there be a well-known URL convention (e.g.
        <code>/.well-known/webmcp-model-cards.json</code>) or should the card be embedded in
        the tool metadata itself?
      </li>
      <li>
        <strong>Versioning alignment:</strong> The WebMCP specification is at Draft Community
        Group Report stage (23 February 2026) with multiple sections marked TODO. This model
        card specification should track changes to the underlying API specification. What
        versioning strategy should be used?
      </li>
      <li>
        <strong>MCP/WebMCP interoperability:</strong> The Agentic AI Foundation (AAIF) and W3C
        are coordinating on aligning common primitives between MCP and WebMCP. Should there be
        a single "Model Card" format with profiles for backend and browser deployment, or should
        the two specifications remain distinct?
      </li>
      <li>
        <strong>Voice agent extensions:</strong> Neither the MCP Model Card v1.0 nor this
        specification addresses real-time voice agent interaction requirements (latency SLAs,
        turn-taking, barge-in handling). Should voice-specific fields be added to a future
        version, or should they be a separate extension specification?
      </li>
      <li>
        <strong>Agent allowlist:</strong> The Web Machine Learning CG is developing requirements
        for enterprise-controlled agent access to WebMCP tools (per the 19 February 2026 meeting
        resolution). How should Model Cards document agent allowlist policies?
      </li>
      <li>
        <strong>Trust and delegation:</strong> Issue #96 in the WebMCP repo addresses agent-to-tool
        trust, identity, scoped permissions, and delegation context. How should the Model Card
        schema represent trust requirements once the specification resolves these questions?
      </li>
    </ol>
  </section>

  <!-- ============================================================ -->
  <!-- SECTION 8: REFERENCES                                        -->
  <!-- ============================================================ -->
  <section id="references" class="informative">
    <h2>References</h2>
    <dl>
      <dt id="biblio-webmcp">[webmcp]</dt>
      <dd>
        Brandon Walderman; Khushal Sagar; Dominic Farolino.
        <a href="https://webmachinelearning.github.io/webmcp/">WebMCP</a>.
        Draft Community Group Report, 23 February 2026.
        W3C Web Machine Learning Community Group.
      </dd>
      <dt id="biblio-mcp">[MCP]</dt>
      <dd>
        <a href="https://modelcontextprotocol.io/specification/latest">Model Context Protocol
        (MCP) Specification</a>. Agentic AI Foundation.
      </dd>
      <dt id="biblio-mcp-model-card-v1">[MCP-MODEL-CARD-V1]</dt>
      <dd>
        Paola Di Maio; Claude.
        <a href="https://claude.ai/chat/8b25ad92-1093-448a-9de6-3197e06316d5">MCP Model Card
        Specification v1.0</a>. January 2026.
        W3C AI Knowledge Representation Community Group.
      </dd>
      <dt>[MITCHELL-2019]</dt>
      <dd>
        Margaret Mitchell et al.
        "Model Cards for Model Reporting."
        <em>Proceedings of the Conference on Fairness, Accountability, and Transparency</em>, 2019.
      </dd>
      <dt>[RFC2119]</dt>
      <dd>
        S. Bradner.
        <a href="https://www.rfc-editor.org/rfc/rfc2119">Key words for use in RFCs to Indicate
        Requirement Levels</a>. BCP 14, RFC 2119, March 1997.
      </dd>
      <dt>[JSON-SCHEMA]</dt>
      <dd>
        <a href="https://json-schema.org/draft/2020-12/json-schema-core.html">JSON Schema:
        A Media Type for Describing JSON Documents</a>.
      </dd>
    </dl>
  </section>

  <!-- ============================================================ -->
  <!-- ACKNOWLEDGEMENTS                                             -->
  <!-- ============================================================ -->
  <section id="acknowledgements" class="informative">
    <h2>Acknowledgements</h2>
    <p>
      This specification builds upon the work of the
      <a href="https://www.w3.org/community/webmachinelearning/">W3C Web Machine Learning
      Community Group</a> and its editors Brandon Walderman, Khushal Sagar, and Dominic Farolino.
      Thanks to Alex Nahas for early implementation experience with browser-based MCP.
      Thanks to the participants of the
      <a href="https://www.w3.org/community/aikr/">W3C AI Knowledge Representation
      Community Group</a> for ongoing discussions on knowledge representation for AI agent
      ecosystems.
    </p>
  </section>

</body>
</html>
