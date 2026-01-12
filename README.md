@lab.Title

## Welcome to Your Lab Environment

To begin, log into the virtual machine using the following credentials: +++@lab.VirtualMachine(Win11-Pro-Base).Password+++

**Note:** Text formatted as an +++example+++ represents type text. Clicking on this text will automatically insert it to prevent any typing errors.

===

# Exercise: Introduction & Environment Setup

## Goal
Set up your development environment, clone the workshop repository, and configure Azure AI Foundry connectivity to prepare for the agent evaluation exercises.

## Learning Objectives
- Clone and open the workshop repository
- Understand the solution structure
- Configure Azure AI Foundry credentials
- Verify the Aspire AppHost starts successfully

## Prerequisites
- **Visual Studio 2026 18.0+** or **Visual Studio Code** with the C# Dev Kit extension
- **.NET 10 SDK** installed
- **Docker Desktop** running (required for Aspire)
- An **Azure subscription** with access to Azure AI Foundry
- **Git** installed and configured

> [+hint] 📚 **Documentation Links:**
>
> [.NET 10 SDK Download](https://dotnet.microsoft.com/download/dotnet/10.0)
>
> [Visual Studio 2026](https://visualstudio.microsoft.com/vs/)
>
> [Docker Desktop](https://www.docker.com/products/docker-desktop/)
>
> [Azure AI Foundry Overview](https://learn.microsoft.com/en-us/azure/ai-studio/what-is-ai-studio)

## Clone the Repository

Choose one of the following methods to clone the workshop repository.

### Option A: Using Terminal / Command Line

1. [ ] Open a terminal or command prompt
1. [ ] Run the following commands:

```bash
git clone https://github.com/seiggy/agent-unit-testing.git
cd agent-unit-testing
```

### Option B: Using Visual Studio Code

1. [ ] Open VS Code
1. [ ] Press ++Ctrl+Shift+P++ (or ++Cmd+Shift+P++ on macOS) to open the Command Palette
1. [ ] Type **Git: Clone** and select it
1. [ ] Paste the repository URL: ++https://github.com/seiggy/agent-unit-testing++
1. [ ] Choose a local folder to clone into
1. [ ] When prompted, click **Open** to open the cloned repository

### Option C: Using Visual Studio 2026

1. [ ] Open Visual Studio 2026
1. [ ] On the start window, select **Clone a repository**
1. [ ] Enter the repository URL: ++https://github.com/seiggy/agent-unit-testing++
1. [ ] Choose a local path and click **Clone**
1. [ ] Visual Studio will automatically open the solution

## Open the Solution

1. [ ] Open the ++AgentEvalsWorkshop.sln++ solution file located in the root of the cloned repository
1. [ ] Familiarize yourself with the solution structure:

> [!knowledge] Solution Structure
> 
> | Project | Purpose |
> |---------|---------|
> | ++AgentEvalsWorkshop++ | Main application containing agent definitions |
> | ++AgentEvalsWorkshop.AppHost++ | .NET Aspire orchestration host |
> | ++AgentEvalsWorkshop.ServiceDefaults++ | Shared service configuration |
> | ++AgentEvalsWorkshop.Tests++ | Integration tests with AI evaluators |

## Start the Aspire AppHost

The Aspire AppHost orchestrates the application and its dependencies, including the Azure AI Foundry connection.

### Using Visual Studio 2026

1. [ ] In **Solution Explorer**, right-click on ++AgentEvalsWorkshop.AppHost++
1. [ ] Select **Set as Startup Project**
1. [ ] Press ++F5++ or click the **Start** button to run
1. [ ] The Aspire Dashboard will open in your default browser

### Using Terminal / VS Code

1. [ ] Open a terminal in the repository root
1. [ ] Run the following command:

```bash
dotnet run --project src/AgentEvalsWorkshop.AppHost
```

> [!knowledge] Dashboard URL
> 
> The terminal will display a URL for the Aspire Dashboard (typically ++https://localhost:17208++ or similar). Open this URL in your browser.

## Configure Azure AI Foundry Credentials

When the Aspire Dashboard opens, you'll need to provide your Azure subscription credentials to connect to Azure AI Foundry.

### Locate Your Azure Subscription ID

1. [ ] Navigate to the [Azure Portal](https://portal.azure.com)
1. [ ] Go to **Subscriptions** in the left navigation or search bar
1. [ ] Copy your **Subscription ID** from the list

### Input Credentials in Aspire Dashboard

1. [ ] In the Aspire Dashboard, locate the ++chat++ resource
1. [ ] Find the configuration prompt or parameters section
1. [ ] Paste your **Subscription ID** into the appropriate field

### Alternative: Configure via User Secrets

> [!knowledge] User Secrets
> 
> If you prefer to configure credentials outside the dashboard, use .NET User Secrets for a more persistent configuration.

1. [ ] Open a terminal and navigate to the AppHost project:

```bash
cd src/AgentEvalsWorkshop.AppHost
```

1. [ ] Set your Azure credentials using the following commands:

```bash
dotnet user-secrets set "Azure:SubscriptionId" "<your-subscription-id>"
dotnet user-secrets set "Azure:ResourceGroup" "<your-resource-group>"
dotnet user-secrets set "Azure:AIFoundry:Endpoint" "<your-foundry-endpoint>"
```

1. [ ] Restart the AppHost after configuring secrets

### Verify Connection

1. [ ] In the Aspire Dashboard, verify the ++chat++ resource transitions to a **Running** (green) state
1. [ ] Verify the ++agents++ resource shows as **Healthy**

> [+hint] 📚 **Documentation Links:**
>
> [.NET Aspire Dashboard](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/dashboard/overview)
>
> [Managing User Secrets](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets)
>
> [Azure AI Foundry Setup](https://learn.microsoft.com/en-us/azure/ai-studio/quickstarts/get-started-playground)

## Verify Your Setup

Run a quick smoke test to ensure everything is configured correctly.

1. [ ] Open a new terminal in the repository root
1. [ ] Run the configuration smoke test:

```bash
dotnet test tests/AgentEvalsWorkshop.Tests --filter "FullyQualifiedName~ConfigurationSmokeTests"
```

1. [ ] Verify the test passes

> [!knowledge] Success Indicators
> 
> A passing test confirms:
> - ✅ Aspire AppHost starts successfully
> - ✅ Azure AI Foundry connection is established
> - ✅ Chat client can be resolved from the service provider

## Troubleshooting

> [!alert] Common Issues
> 
> | Symptom | Possible Cause | Resolution |
> |---------|----------------|------------|
> | AppHost fails to start | Docker not running | Start Docker Desktop and wait for it to be fully running |
> | ++chat++ resource stays in "Starting" | Missing or invalid Azure credentials | Verify Subscription ID and Foundry endpoint in user secrets or dashboard |
> | Connection timeout | Network/firewall issues | Ensure outbound access to Azure services; check VPN settings |
> | "Subscription not found" error | Incorrect Subscription ID | Double-check the ID in Azure Portal under Subscriptions |
> | Dashboard doesn't open | Port conflict | Check terminal output for the actual dashboard URL |

## Success Criteria

You're ready to proceed to the exercises when:

- [ ] Repository is cloned and solution opens without errors
- [ ] Aspire AppHost starts and the dashboard is accessible
- [ ] Azure credentials are configured (via dashboard or user secrets)
- [ ] The ++chat++ and ++agents++ resources show as Running/Healthy in the dashboard
- [ ] Configuration smoke tests pass


Once your environment is set up, proceed to the next exercise below.

===

# Exercise: TaskAdherenceEvaluator

## Goal
Create a test class that uses the **TaskAdherenceEvaluator** to evaluate how well an AI agent accomplishes a given task. You will build a Weather Assistant Agent test that verifies the agent correctly uses its available tools to respond to user requests.

## Learning Objectives
- Understand how to configure AI evaluation reporting
- Learn to use the _TaskAdherenceEvaluator_ to measure agent performance
- Practice writing integration tests for AI agents
- Interpret evaluation metrics and assertions

## Prerequisites
- Aspire AppHost configured run once
- Solution open in Visual Studio 2026 or VS Code
- Azure Foundry endpoint configured in user secrets or environment variables

## Key Concepts

The **TaskAdherenceEvaluator** measures how well an AI agent follows instructions and uses its available tools to complete a task. It examines:
- Whether the agent called the appropriate tools
- Whether the agent's response aligns with the task requirements
- The quality of the agent's tool usage decisions

📚 **Documentation Links:**
- [TaskAdherenceEvaluator Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.quality.taskadherenceevaluator?view=net-10.0-pp)
- [AI Evaluation Library Overview](https://learn.microsoft.com/en-us/dotnet/ai/evaluation/libraries)

## Create the Test Class

1. [ ] Create a new file **WeatherAssistantAgentTests.cs** in the **tests/AgentEvalsWorkshop.Tests/** project.

1. [ ] Add the necessary **using** directives for the ++AgentEvalsWorkshop.Agents++ namespace, test helpers, and the ++Microsoft.Extensions.AI.Evaluation++ packages (including `Quality`, `Reporting`, and `Reporting.Storage` namespaces)
1. [ ] Define a test class decorated with ++[TestClass]++ that inherits from ++BaseIntegrationTest++

> [!knowledge] BaseIntegrationTest
> 
> The base class provides a **ServiceProvider**, **ChatConfiguration**, **ScenarioName**, **ExecutionName**, and **Evaluators**
> that will be used in your tests.

> [+hint] 📚 **Documentation Links:**
>
> [MSTest v3](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-mstest-writing-tests)

<details>
<summary>💡 Show Example Implementation</summary>

```csharp
using AgentEvalsWorkshop.Agents;
using AgentEvalsWorkshop.Tests.Helpers;
using Aspire.Hosting;
using Microsoft.Extensions.AI;
using Microsoft.Extensions.AI.Evaluation;
using Microsoft.Extensions.AI.Evaluation.Quality;
using Microsoft.Extensions.AI.Evaluation.Reporting;
using Microsoft.Extensions.AI.Evaluation.Reporting.Storage;
using System;
using System.Collections.Generic;
using System.Text;

namespace AgentEvalsWorkshop.Tests;

[TestClass]
public class WeatherAssistantAgentTests : BaseIntegrationTest
{
    // Fields and methods will be added in subsequent steps
}
```

</details>



##  Add the Reporting Configuration

1. [ ] Declare a static nullable field of type ++ReportingConfiguration++ to store the evaluation reporting configuration
> [!knowledge] This field will be initialized during class setup and shared across all tests in the class

> [+hint] 📚 **Documentation Links:**
>
> [Evaluate with Reporting Learn Tutorial](https://learn.microsoft.com/en-us/dotnet/ai/evaluation/evaluate-with-reporting)

<details>
<summary>💡 Show Example Implementation</summary>

```csharp
private static ReportingConfiguration? s_defaultReportingConfiguration;
```

</details>

##  Create the Evaluators Factory

1. [ ] Create a *private static* method named ++GetEvaluators++ that returns `IEnumerable<IEvaluator>`
1. [ ] Instantiate a new **TaskAdherenceEvaluator** and return it in a collection
> [!knowledge] This method encapsulates which evaluators your tests will use

> [+hint] 📚 **Documentation Links:**
>
> [Sample Agent Evaluator Unit Tests](https://github.com/dotnet/extensions/blob/main/test/Libraries/Microsoft.Extensions.AI.Evaluation.Integration.Tests/AgentQualityEvaluatorTests.cs)

<details>
<summary>💡 Show Example Implementation</summary>

```csharp
private static IEnumerable<IEvaluator> GetEvaluators()
{
    var taskAdherenceEvaluator = new TaskAdherenceEvaluator();
    return [ taskAdherenceEvaluator ];
}
```

</details>

## Implement Class Initialization

1. [ ] Add a `public static async Task` method decorated with `[ClassInitialize]` that accepts a `TestContext` parameter
1. [ ] Assign the result of `GetEvaluators()` to the inherited `Evaluators` property
1. [ ] Use `DiskBasedReportingConfiguration.Create()` to configure reporting with:
  - A `storageRootPath` for persisting HTML reports (e.g., `C:\TestReports`)
  - Your `Evaluators` collection
  - The inherited `ChatConfiguration` for evaluator LLM calls
  - `enableResponseCaching` set to `true` for deterministic reruns
  - The inherited `ExecutionName` to group test runs

> [+hint] 📚 **Documentation Links:**
>
> [Unit Testing Lifecycles](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-mstest-writing-tests-lifecycle#class-level-lifecycle)
>
> [DiskBasedReportingConfiguration Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.reporting.storage.diskbasedreportingconfiguration?view=net-10.0-pp)

<details>
<summary>💡 Show Example Implementation</summary>

```csharp
[ClassInitialize]
public static async Task ClassInitialize(TestContext context)
{
    Evaluators = GetEvaluators();
    s_defaultReportingConfiguration = DiskBasedReportingConfiguration.Create(
            storageRootPath: "C:\\TestReports",
            evaluators: Evaluators,
            chatConfiguration: ChatConfiguration,
            enableResponseCaching: true,
            executionName: ExecutionName
        );
}
```

</details>

## Write the Test Method

### Arrange
1. [ ] Decorate your method with `[TestMethod]` and make it `public async Task`
1. [ ] Create a `ScenarioRun` by calling `CreateScenarioRunAsync()` on your reporting configuration, passing `ScenarioName` and optional tags (use `await using` for proper disposal)
1. [ ] Create a scoped `IServiceProvider` from the inherited `ServiceProvider` and resolve an `IChatClient`
1. [ ] Build the agent using `US1Agent.BuildUS1Agent()`, passing the chat client
1. [ ] Create a `TaskAdherenceEvaluatorContext` with the agent's tool definitions from `US1Agent.GetToolDefinitions()`

### Act
1. [ ] Define a user message string that should trigger the weather tool (e.g., asking about weather)
1. [ ] Call `agent.RunAsync()` with the message and a cancellation token from `TestContext.CancellationTokenSource`
1. [ ] Call `scenarioRun.EvaluateAsync()` passing:
  - The `Messages` from the agent response
  - The response converted via the `ToChatResponse()` extension method
  - The tool definitions context in the `additionalContext` array
  - The cancellation token

### Assert
1. [ ] Extract the `NumericMetric` from results using `result.Get<NumericMetric>()` with `TaskAdherenceEvaluator.TaskAdherenceMetricName`
1. [ ] Assert that `Interpretation.Failed` is `false` (use the metric's `Reason` as the failure message)
1. [ ] Assert that `Interpretation.Rating` is either `EvaluationRating.Good` or `EvaluationRating.Exceptional`

> [+hint] 📚 **Documentation Links:**
>
> [ScenarioRun Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.reporting.reportingconfiguration.createscenariorunasync?view=net-10.0-pp)
>
> [Evaluator Examples](https://github.com/dotnet/ai-samples/tree/main/src/microsoft-extensions-ai-evaluation/api/evaluation)

<details>
<summary>💡 Show Example Implementation</summary>

```csharp
[TestMethod]
public async Task DoesPersonalAgentRetrieveWeather()
{
    // Arrange
    await using ScenarioRun scenarioRun =
        await s_defaultReportingConfiguration!
        .CreateScenarioRunAsync(ScenarioName,
        additionalTags: ["Weather", "Agent"]);
    using var scope = ServiceProvider!.CreateScope();
    var chatClient = scope.ServiceProvider.GetRequiredService<IChatClient>();
    var agent = US1Agent.BuildUS1Agent(chatClient);

    var toolDefinitionsForTaskAdherenceEvaluator =
        new TaskAdherenceEvaluatorContext(toolDefinitions: US1Agent.GetToolDefinitions());

    // Act
    var userMessage = "What's the weather like today?";
    var response = await agent.RunAsync(userMessage,
        cancellationToken: TestContext!.CancellationTokenSource.Token);

    var result = await scenarioRun.EvaluateAsync(
        messages: response.Messages,
        modelResponse: response.ToChatResponse(),
        additionalContext: [toolDefinitionsForTaskAdherenceEvaluator],
        cancellationToken: TestContext!.CancellationTokenSource.Token);

    // Assert
    NumericMetric taskAdherance = result.Get<NumericMetric>(TaskAdherenceEvaluator.TaskAdherenceMetricName);
    Assert.IsFalse(taskAdherance.Interpretation!.Failed, taskAdherance.Reason);
    Assert.IsTrue(taskAdherance.Interpretation.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);
}
```

</details>

##  Run and Validate

1. [ ] Build the solution to verify there are no compilation errors
1. [ ] Execute the test using the `dotnet test` CLI with a filter for your test class, or use Visual Studio Test Explorer

    ```Powershell
    dotnet test tests/AgentEvalsWorkshop.Tests --filter "FullyQualifiedName~WeatherAssistantAgentTests"
    ```

    @[Click this to run the unit tests][Run Unit Tests]{Powershell}

[Run Unit Tests]:
```Powershell
cd c:\github\agent-unit-testing
dotnet test tests/AgentEvalsWorkshop.Tests --filter "FullyQualifiedName~WeatherAssistantAgentTests"
```

> [+hint] 📚 **Documentation Links:**
>
> [dotnet test](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-test-vstest)

## Generate and View the Evaluation Report

1. [ ] After the test completes successfully, use the `aieval` CLI tool to generate an HTML report from the cached evaluation data
1. [ ] Run the following command from the root of your repository to generate a consolidated report:

    ```Powershell
    cd c:\github\agent-unit-testing
    dotnet aieval report -p C:\TestReports -o test-report.html
    ```

    @[Click this to run the test report][AIEval Report]{Powershell}

[AIEval Report]:
```Powershell
cd c:\github\agent-unit-testing
dotnet aieval report -p C:\TestReports -o test-report.html
```

3. [ ] Open `test-report.html` in your browser to view the detailed evaluation results

**What you'll see in the report:**

!IMAGE[weather-agent-eval-report.png](instructions333020/weather-agent-eval-report.png)

- **Breadcrumb Navigation**: Hierarchical path showing All Evaluations → Namespace → Test Class → Test Method with pass/fail counts (e.g., `1/1 [100.0%]`)
- **Trends Panel**: Collapsible section for viewing evaluation trends over time
- **Task Adherence Score**: A highlighted metric card showing the numeric score (e.g., `5`) with a visual indicator of success
- **Metric Details**: Expanded view containing:
  - **Evaluation Reason**: Plain-language explanation of why the score was assigned (e.g., "The response is clear, accurate, and adheres to the instructions by retrieving the weather data...")
  - **Diagnostics**: The evaluator's chain-of-thought reasoning showing step-by-step analysis of tool usage and response quality
- **Metadata Table**: Key-value pairs including:
  - `built-in-eval`: Whether the evaluator is built-in
  - `eval-model`: The model used for evaluation (e.g., `gpt-4o-2024-11-20`)
  - `eval-input-tokens` / `eval-output-tokens` / `eval-total-tokens`: Token usage for the evaluation
  - `eval-duration-ms`: Time taken for the evaluation
- **Conversation Panel**: Collapsible section showing token counts and the full message exchange
- **Diagnostic Data Table**: Cache status, latency, model provider details, and token breakdown for each evaluation call

📚 **Documentation Links:**
- [aieval report tool](https://learn.microsoft.com/en-us/dotnet/ai/evaluation/evaluate-with-reporting#generate-a-report)
- [Interpreting Evaluation Reports](https://learn.microsoft.com/en-us/dotnet/ai/evaluation/libraries#interpret-results)

---

## Success Criteria

A passing test indicates:
- ✅ The agent correctly identified it needed to retrieve weather information
- ✅ The agent called the `GetWeatherForecast` tool
- ✅ The agent's response addressed the user's question
- ✅ Task adherence rating is `Good` or `Exceptional`

---

## Troubleshooting

| Symptom | Possible Cause | Resolution |
|---------|----------------|------------|
| `NullReferenceException` on `s_defaultReportingConfiguration` | `ClassInitialize` didn't run | Verify the `[ClassInitialize]` attribute is present and the method signature is correct |
| `ServiceProvider` is `null` | Assembly initialization failed | Check that `BaseIntegrationTest.BaseInitialize` completed successfully; review Aspire app host logs |
| `ChatConfiguration` throws | Missing or invalid connection string | Verify Azure Foundry credentials in user secrets or environment variables |
| Low task adherence scores | Agent instructions don't align with task | Review the system prompt in `US1Agent.cs`; ensure tool definitions are correctly exposed |
| Report directory empty | Write permission denied | Verify your user has write access to the `storageRootPath` directory |

---

## Extension Challenges

1. **Multiple Evaluators**: Add `RelevanceEvaluator` or `CoherenceEvaluator` to your evaluator collection and observe how scores differ
2. **Edge Case Testing**: Create additional test methods for prompts like "What's the temperature in Celsius?" and evaluate how the agent handles unit conversion expectations
3. **Threshold Tuning**: Modify your assertions to require specific numeric score thresholds instead of rating enums
4. **Parameterized Tests**: Use `[DataRow]` to run the same test logic against multiple weather-related prompts

📚 **Documentation Links:**
- [Sample Evaluators](https://github.com/dotnet/ai-samples/blob/main/src/microsoft-extensions-ai-evaluation/api/evaluation/README.md)
- [Data Driven Unit Tests](https://learn.microsoft.com/en-us/visualstudio/test/how-to-create-a-data-driven-unit-test?view=visualstudio)

---

## Complete Solution

<details>
<summary>📄 Show Complete WeatherAssistantAgentTests.cs</summary>

```csharp
using AgentEvalsWorkshop.Agents;
using AgentEvalsWorkshop.Tests.Helpers;
using Aspire.Hosting;
using Microsoft.Extensions.AI;
using Microsoft.Extensions.AI.Evaluation;
using Microsoft.Extensions.AI.Evaluation.Quality;
using Microsoft.Extensions.AI.Evaluation.Reporting;
using Microsoft.Extensions.AI.Evaluation.Reporting.Storage;
using System;
using System.Collections.Generic;
using System.Text;

namespace AgentEvalsWorkshop.Tests;

[TestClass]
public class WeatherAssistantAgentTests : BaseIntegrationTest
{
    private static ReportingConfiguration? s_defaultReportingConfiguration;

    [ClassInitialize]
    public static async Task ClassInitialize(TestContext context)
    {
        Evaluators = GetEvaluators();
        s_defaultReportingConfiguration = DiskBasedReportingConfiguration.Create(
                storageRootPath: "C:\\TestReports",
                evaluators: Evaluators,
                chatConfiguration: ChatConfiguration,
                enableResponseCaching: true,
                executionName: ExecutionName
            );
    }

    private static IEnumerable<IEvaluator> GetEvaluators()
    {
        var taskAdherenceEvaluator = new TaskAdherenceEvaluator();
        return [ taskAdherenceEvaluator ];
    }

    [TestMethod]
    public async Task DoesPersonalAgentRetrieveWeather()
    {
        // Arrange
        await using ScenarioRun scenarioRun =
            await s_defaultReportingConfiguration!
            .CreateScenarioRunAsync(ScenarioName,
            additionalTags: ["Weather", "Agent"]);
        using var scope = ServiceProvider!.CreateScope();
        var chatClient = scope.ServiceProvider.GetRequiredService<IChatClient>();
        var agent = US1Agent.BuildUS1Agent(chatClient);

        var toolDefinitionsForTaskAdherenceEvaluator =
            new TaskAdherenceEvaluatorContext(toolDefinitions: US1Agent.GetToolDefinitions());

        // Act
        var userMessage = "What's the weather like today?";
        var response = await agent.RunAsync(userMessage,
            cancellationToken: TestContext!.CancellationTokenSource.Token);

        var result = await scenarioRun.EvaluateAsync(
            messages: response.Messages,
            modelResponse: response.ToChatResponse(),
            additionalContext: [toolDefinitionsForTaskAdherenceEvaluator],
            cancellationToken: TestContext!.CancellationTokenSource.Token);

        // Assert
        NumericMetric taskAdherance = result.Get<NumericMetric>(TaskAdherenceEvaluator.TaskAdherenceMetricName);
        Assert.IsFalse(taskAdherance.Interpretation!.Failed, taskAdherance.Reason);
        Assert.IsTrue(taskAdherance.Interpretation.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);
    }
}
```

</details>

===

# Exercise: Retrieval Evaluation with Built-in Evaluators

## Goal
Create a test class that evaluates a Knowledgebase Chat Agent using **built-in evaluators** (Relevance, Coherence, Groundedness). You will learn how to use multiple evaluators together to comprehensively assess AI response quality against a knowledge base.

## Learning Objectives
- Understand how to use multiple built-in evaluators together for comprehensive evaluation
- Use **data-driven tests** to evaluate multiple question/answer scenarios
- Work with **GroundednessEvaluatorContext** to validate responses against a knowledge base
- Interpret evaluation metrics from multiple evaluators simultaneously

## Prerequisites
- Exercise US1 completed successfully
- Aspire AppHost configured and running
- Solution open in Visual Studio 2026 or VS Code
- Azure Foundry endpoint configured in user secrets or environment variables

## Key Concepts

This exercise combines several built-in evaluators to assess AI response quality:

| Evaluator | Purpose |
|-----------|---------|
| **RelevanceEvaluator** | Measures how relevant the response is to the user's question |
| **CoherenceEvaluator** | Measures how well-structured and coherent the response is |
| **GroundednessEvaluator** | Measures whether the response is grounded in provided context/facts |

📚 **Documentation Links:**
- [RelevanceEvaluator Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.quality.relevanceevaluator?view=net-10.0-pp)
- [CoherenceEvaluator Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.quality.coherenceevaluator?view=net-10.0-pp)
- [GroundednessEvaluator Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.quality.groundednessevaluator?view=net-10.0-pp)

---

## Create the Test File

1. [ ] Create a new file **AgentRetrievalEvalTests.cs** in the **tests/AgentEvalsWorkshop.Tests/** project

1. [ ] Add the necessary **using** directives:
   - `AgentEvalsWorkshop.Agents` for the agent
   - `AgentEvalsWorkshop.Tests.Helpers` for test helpers
   - `Microsoft.Agents.AI` for the AI agent framework
   - `Microsoft.Extensions.AI` for chat client interfaces
   - `Microsoft.Extensions.AI.Evaluation` and sub-namespaces for evaluators
   - Add `ChatRole = Microsoft.Extensions.AI.ChatRole` alias to avoid ambiguity

1. [ ] Add the pragma directive `#pragma warning disable AIEVAL001` at the top to suppress preview API warnings

1. [ ] Define a test class decorated with `[TestClass]` that inherits from `BaseIntegrationTest`

<details>
<summary>💡 Show Class Setup</summary>

```csharp
#pragma warning disable AIEVAL001
using AgentEvalsWorkshop.Agents;
using AgentEvalsWorkshop.Tests.Helpers;
using Microsoft.Agents.AI;
using Microsoft.Extensions.AI;
using Microsoft.Extensions.AI.Evaluation;
using Microsoft.Extensions.AI.Evaluation.Quality;
using Microsoft.Extensions.AI.Evaluation.Reporting;
using Microsoft.Extensions.AI.Evaluation.Reporting.Storage;
using ChatRole = Microsoft.Extensions.AI.ChatRole;

namespace AgentEvalsWorkshop.Tests;

[TestClass]
public class AgentRetrievalEvalTests : BaseIntegrationTest
{
    private static ReportingConfiguration? s_defaultReportingConfiguration;
    
    // Methods will be added in subsequent steps
}
```

</details>

## Add the Reporting Configuration

1. [ ] Declare a static nullable field of type `ReportingConfiguration` to store the evaluation reporting configuration

> [!knowledge] This field will be initialized during class setup and shared across all tests in the class

<details>
<summary>💡 Show Field Declaration</summary>

```csharp
private static ReportingConfiguration? s_defaultReportingConfiguration;
```

</details>

## Create the Evaluators Factory

1. [ ] Create a `private static` method named `GetEvaluators` that returns `IEnumerable<IEvaluator>`
1. [ ] Instantiate the three built-in evaluators:
   - `RelevanceEvaluator`
   - `CoherenceEvaluator`
   - `GroundednessEvaluator`
1. [ ] Return them in a collection

<details>
<summary>💡 Show GetEvaluators Implementation</summary>

```csharp
private static IEnumerable<IEvaluator> GetEvaluators()
{
    var relevanceEvaluator = new RelevanceEvaluator();
    var coherenceEvaluator = new CoherenceEvaluator();
    var groundednessEvaluator = new GroundednessEvaluator();
    return [ relevanceEvaluator, coherenceEvaluator, groundednessEvaluator ];
}
```

</details>

## Implement Class Initialization

1. [ ] Add a `public static async Task` method decorated with `[ClassInitialize]` that accepts a `TestContext` parameter
1. [ ] Assign the result of `GetEvaluators()` to the inherited `Evaluators` property
1. [ ] Use `DiskBasedReportingConfiguration.Create()` to configure reporting with:
   - A `storageRootPath` for persisting HTML reports (e.g., `C:\TestReports`)
   - Your `Evaluators` collection
   - The inherited `ChatConfiguration` for evaluator LLM calls
   - `enableResponseCaching` set to `true` for deterministic reruns
   - The inherited `ExecutionName` to group test runs

> [+hint] 📚 **Documentation Links:**
>
> [Unit Testing Lifecycles](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-mstest-writing-tests-lifecycle#class-level-lifecycle)
>
> [DiskBasedReportingConfiguration Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.reporting.storage.diskbasedreportingconfiguration?view=net-10.0-pp)

<details>
<summary>💡 Show ClassInitialize Implementation</summary>

```csharp
[ClassInitialize]
public static async Task ClassInitialize(TestContext context)
{
    Evaluators = GetEvaluators();
    s_defaultReportingConfiguration = DiskBasedReportingConfiguration.Create(
            storageRootPath: "C:\\TestReports",
            evaluators: Evaluators,
            chatConfiguration: ChatConfiguration,
            enableResponseCaching: true,
            executionName: ExecutionName
        );
}
```

</details>

## Create the Knowledge Base Helper

1. [ ] Create a `private static async Task<string>` method named `GetKnowledgebaseContext`
1. [ ] Read the CSV file from `./Data/Gamepass_Games_v1.csv`
1. [ ] Return a formatted string containing the knowledge base context

> [!knowledge] GroundednessEvaluator Context
> 
> The `GroundednessEvaluator` needs a reference to the source data to verify the AI's response is grounded in facts. This helper provides that context.

<details>
<summary>💡 Show GetKnowledgebaseContext Implementation</summary>

```csharp
private static async Task<string> GetKnowledgebaseContext()
{
    var csvData = await File.ReadAllLinesAsync("./Data/Gamepass_Games_v1.csv");

    return $"""
        The following is the knowledge base about Xbox Gamepass games in CSV format:
        {string.Join("\n", csvData)}
        """;
}
```

</details>

## Create the EvalQuestion Record

1. [ ] Add an `EvalQuestion` record to hold question/answer pairs for testing:

```csharp
record EvalQuestion(int QuestionId, string Question, string Answer);
```

## Create the Evaluation Helper Method

1. [ ] Create a `private static async Task` method named `EvaluateQuestion` with parameters:
   - `EvalQuestion question`
   - `ReportingConfiguration reportingConfiguration`
   - `AIAgent agent`
   - `CancellationToken cancellationToken`

1. [ ] Create a `ScenarioRun` for each question using `CreateScenarioRunAsync()`

1. [ ] Create a new thread for the agent and build the chat history

1. [ ] Run the agent and collect the response

1. [ ] Call `scenario.EvaluateAsync()` passing:
   - The user's question as a `ChatMessage`
   - The agent response converted via `ToChatResponse()`
   - Additional context with `GroundednessEvaluatorContext` containing the knowledge base

<details>
<summary>💡 Show EvaluateQuestion Implementation</summary>

```csharp
private static async Task EvaluateQuestion(
    EvalQuestion question, 
    ReportingConfiguration reportingConfiguration, 
    AIAgent agent,
    CancellationToken cancellationToken)
{
    // Create a Scenario Run for each question.
    await using ScenarioRun scenario = await reportingConfiguration.CreateScenarioRunAsync($"Question_{question.QuestionId}", cancellationToken: cancellationToken);

    // create a thread to track the Q&A interaction
    var thread = agent.GetNewThread();
    var chatHistory = new List<ChatMessage>
    {
        new ChatMessage(ChatRole.User, question.Question)
    };

    var response = await agent.RunAsync(
        chatHistory,
        thread: thread,
        cancellationToken: cancellationToken
    );
    chatHistory.AddRange(response.Messages);

    var result = await scenario.EvaluateAsync(
        messages: [new ChatMessage(ChatRole.User, question.Question)],
        modelResponse: response.ToChatResponse(),
        additionalContext: [new GroundednessEvaluatorContext(await GetKnowledgebaseContext())],
        cancellationToken: cancellationToken
    );

    Validate(result);
}
```

</details>

## Create the Validation Helper

1. [ ] Create a `private static void` method named `Validate` that accepts an `EvaluationResult`
1. [ ] Retrieve and assert on all three metrics:
   - **Relevance**: Assert not failed and rating is `Good` or `Exceptional`
   - **Coherence**: Assert not failed and rating is `Good` or `Exceptional`
   - **Groundedness**: Assert not failed and rating is `Good` or `Exceptional`

<details>
<summary>💡 Show Validate Implementation</summary>

```csharp
private static void Validate(EvaluationResult result)
{
    // Retrieve the score for relevance from the EvaluationResult.
    NumericMetric relevance =
        result.Get<NumericMetric>(RelevanceEvaluator.RelevanceMetricName);
    Assert.IsFalse(relevance.Interpretation?.Failed, relevance.Reason);
    Assert.IsTrue(relevance.Interpretation?.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);

    // Retrieve the score for coherence from the EvaluationResult.
    NumericMetric coherence =
        result.Get<NumericMetric>(CoherenceEvaluator.CoherenceMetricName);
    Assert.IsFalse(coherence.Interpretation?.Failed, coherence.Reason);
    Assert.IsTrue(coherence.Interpretation?.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);
            
    // Retrieve the score for groundedness from the EvaluationResult.
    NumericMetric groundedness =
        result.Get<NumericMetric>(GroundednessEvaluator.GroundednessMetricName);
    Assert.IsFalse(groundedness.Interpretation?.Failed, groundedness.Reason);
    Assert.IsTrue(groundedness.Interpretation?.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);
}
```

</details>

## Write the Data-Driven Test Method

1. [ ] Decorate your method with `[TestMethod]` and multiple `[DataRow]` attributes for different test cases
1. [ ] Accept parameters: `int questionId`, `string questionText`, `string expectedAnswer`
1. [ ] Create an `EvalQuestion` record from the parameters
1. [ ] Resolve the `IChatClient` and build the `KnowledgebaseChatAgent`
1. [ ] Call `EvaluateQuestion()` with all dependencies

> [!knowledge] Data-Driven Tests
> 
> Using `[DataRow]` attributes allows you to run the same test logic against multiple input/output scenarios without duplicating code.

<details>
<summary>💡 Show Test Method Implementation</summary>

```csharp
[TestMethod]
[DataRow(1, "What game can I quickly play to get 1000 gamerscore?", "The shortest known game to achieve 1000 gamerscore on Xbox Gamepass is 'Townscaper', which can be completed in approximately 30 minutes!")]
[DataRow(2, "Which game on Xbox Gamepass has the highest completion rate?", "The game with the highest completion rate on Xbox Gamepass is 'The Walking Dead: Michonne' with a completion rate of 84.7%!")]
[DataRow(3, "What do gamers think of 'Forza Horizon 5'?", "Forza Horizon 5 currently has a rating of 4.5 out of 5 stars! Gamers love this game!")]
[DataRow(4, "How long does it typically take to complete 'Halo Infinite'?", "On average, it takes about 100-120 hours to complete all challenges of 'Halo Infinite'.")]
[DataRow(5, "I'm looking for a game that will take me a long time to finish, preferably an RPG. Any suggestions?", "'Black Desert' is a great choice for a long RPG experience, with an average completion time of 500-750 hours to complete all gamerscope challenges!")]
public async Task KnowledgebaseChatAgent_EvaluateQuestionAnswer_Scores(int questionId, string questionText, string expectedAnswer)
{
    var question = new EvalQuestion(questionId, questionText, expectedAnswer);
    using var scope = ServiceProvider!.CreateScope();
    var chatClient = scope.ServiceProvider.GetRequiredService<IChatClient>();
    var agent = KnowledgebaseChatAgent.BuildKnowledgebaseChatAgent(chatClient);

    await EvaluateQuestion(
        question,
        s_defaultReportingConfiguration!,
        agent,
        TestContext!.CancellationTokenSource.Token);
}
```

</details>

---

## Run and Validate

1. [ ] Build the solution to verify there are no compilation errors
1. [ ] Execute the test using the `dotnet test` CLI with a filter for your test class:

	```Powershell
	dotnet test tests/AgentEvalsWorkshop.Tests --filter "FullyQualifiedName~AgentRetrievalEvalTests"
	```

	@[Click this to run the unit tests][Run Unit Tests]{Powershell}

[Run Unit Tests]:
```Powershell
cd d:\github\seiggy\agent-unit-testing
dotnet test tests/AgentEvalsWorkshop.Tests --filter "FullyQualifiedName~AgentRetrievalEvalTests"
```

## Generate and View the Evaluation Report

1. [ ] After the tests complete, use the `aieval` CLI tool to generate an HTML report:

	```Powershell
	dotnet aieval report -p C:\TestReports -o retrieval-eval-report.html
	```

	@[Click this to run the test report][AIEval Report]{Powershell}

[AIEval Report]:
```Powershell
cd d:\github\seiggy\agent-unit-testing
dotnet aieval report -p C:\TestReports -o retrieval-eval-report.html
```

2. [ ] Open `retrieval-eval-report.html` in your browser to view the detailed evaluation results

**What you'll see in the report:**

- **Multiple Metrics Per Scenario**: Each question shows scores for Relevance, Coherence, and Groundedness
- **Aggregated Results**: Overall pass/fail rates across all data rows
- **Detailed Diagnostics**: For each evaluator, explanations of why scores were assigned
- **Conversation History**: The full exchange between user and agent

📚 **Documentation Links:**
- [aieval report tool](https://learn.microsoft.com/en-us/dotnet/ai/evaluation/evaluate-with-reporting#generate-a-report)

---

## Success Criteria

A passing test suite indicates:
- ✅ The agent correctly retrieves information from the knowledge base
- ✅ Responses are relevant to the user's questions
- ✅ Responses are coherent and well-structured
- ✅ Responses are grounded in the actual CSV data
- ✅ All ratings are `Good` or `Exceptional`

---

## Troubleshooting

| Symptom | Possible Cause | Resolution |
|---------|----------------|------------|
| `FileNotFoundException` for CSV | Incorrect path or missing data file | Ensure `./Data/Gamepass_Games_v1.csv` exists relative to test output directory |
| Groundedness fails | Response contains fabricated information | Check that the agent is using the knowledge base tool correctly |
| `NullReferenceException` on ChatConfiguration | Assembly initialization failed | Ensure Aspire AppHost is running and connection string is valid |
| All evaluators return `Inconclusive` | LLM didn't respond in expected format | Check ChatConfiguration and model availability |
| Relevance score is low | Response doesn't address the question | Review agent instructions and tool usage |

---

## Next Steps

In the next exercise (US3), you will create a **custom evaluator** to compare the agent's responses against expected answers, giving you more control over domain-specific evaluation criteria.

===

# Exercise: Creating a Custom Evaluator

## Goal
Create a **custom AnswerScoringEvaluator** that compares AI responses against expected answers, then integrate it into your existing test suite. You will learn how to build domain-specific evaluators using the `IEvaluator` interface and the LLM-as-Judge pattern.

## Learning Objectives
- Understand the `IEvaluator` interface and how to implement it
- Learn to create custom **EvaluationContext** classes for passing additional data
- Use the **LLM-as-Judge** pattern to evaluate AI responses
- Integrate custom evaluators with built-in evaluators in a test suite
- Work with structured output from LLMs using `GetResponseAsync<T>()`

## Prerequisites
- Exercise US2 completed successfully
- Aspire AppHost configured and running
- Solution open in Visual Studio 2026 or VS Code
- Azure Foundry endpoint configured in user secrets or environment variables

## Key Concepts

Custom evaluators allow you to define domain-specific evaluation criteria that go beyond the built-in evaluators. This exercise implements an **Answer Scoring Evaluator** that:

| Component | Purpose |
|-----------|---------|
| **IEvaluator Interface** | Standard contract for all evaluators |
| **EvaluationContext** | Passes additional data (expected answer) to the evaluator |
| **LLM-as-Judge** | Uses an LLM to compare responses and assign scores |
| **NumericMetric** | Returns a score from 1-5 with interpretation |

📚 **Documentation Links:**
- [Creating Custom Evaluators](https://learn.microsoft.com/en-us/dotnet/ai/evaluation/evaluate-with-reporting#create-custom-evaluators)
- [IEvaluator Interface](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.ievaluator?view=net-10.0-pp)
- [EvaluationContext Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.evaluationcontext?view=net-10.0-pp)

---

## Create the Evaluator File

1. [ ] Create a new file **AnswerScoringEvaluator.cs** in the **tests/AgentEvalsWorkshop.Tests/Helpers/** folder

1. [ ] Add the necessary **using** directives and place the class in the `Microsoft.Extensions.AI.Evaluation.Quality` namespace

> [!knowledge] Why this namespace?
> 
> Placing your custom evaluator in the same namespace as the built-in evaluators allows seamless integration and discoverability alongside `RelevanceEvaluator`, `CoherenceEvaluator`, etc.

<details>
<summary>💡 Show Namespace Setup</summary>

```csharp
// --------------------------------------------------------------------------------------------
// Copyright (c) Microsoft Corporation. All rights reserved.
// --------------------------------------------------------------------------------------------

namespace Microsoft.Extensions.AI.Evaluation.Quality;
```

</details>

## Define the Evaluator Class

1. [ ] Create a `public sealed class` named `AnswerScoringEvaluator` that implements `IEvaluator`
1. [ ] Define a private constant string `MetricName` with value `"Answer Score"`
1. [ ] Create a public constant `AnswerScoreMetricName` that exposes the metric name
1. [ ] Implement the `EvaluationMetricNames` property that returns a collection containing your metric name

> [!knowledge] IEvaluator Interface
> 
> The `IEvaluator` interface requires:
> - `EvaluationMetricNames`: Declares which metrics this evaluator produces
> - `EvaluateAsync()`: The main evaluation logic

<details>
<summary>💡 Show Class Structure</summary>

```csharp
public sealed class AnswerScoringEvaluator : IEvaluator
{
    private const string MetricName = "Answer Score";

    public const string AnswerScoreMetricName = MetricName;

    public IReadOnlyCollection<string> EvaluationMetricNames => [MetricName];

    // EvaluateAsync will be added next
}
```

</details>

## Create the Context Class

1. [ ] Inside `AnswerScoringEvaluator`, create a `public sealed class` named `Context` that inherits from `EvaluationContext`
1. [ ] The constructor should accept a `string expectedAnswer` parameter
1. [ ] Call the base constructor with a context name and the expected answer as content
1. [ ] Expose the expected answer through a public property `ExpectedAnswer`

> [!knowledge] EvaluationContext
> 
> Custom context classes allow you to pass additional information to your evaluator. This is how we'll provide the expected answer for comparison.

<details>
<summary>💡 Show Context Implementation</summary>

```csharp
public sealed class Context(string expectedAnswer) : EvaluationContext(ContextName, content: expectedAnswer)
{
    private const string ContextName = "Answer Score";

    public string ExpectedAnswer { get; } = expectedAnswer;
}
```

</details>

## Implement the EvaluateAsync Method Signature

1. [ ] Add the `EvaluateAsync` method with the standard `IEvaluator` signature:
   - `IEnumerable<ChatMessage> messages`
   - `ChatResponse modelResponse`
   - `ChatConfiguration? chatConfiguration`
   - `IEnumerable<EvaluationContext>? additionalContext`
   - `CancellationToken cancellationToken`

1. [ ] Validate that `modelResponse` and `chatConfiguration` are not null using `ArgumentNullException.ThrowIfNull()`

1. [ ] Create a `NumericMetric` with your metric name and wrap it in an `EvaluationResult`

> [+hint] 📚 **Documentation Links:**
>
> [EvaluationResult Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.ai.evaluation.evaluationresult?view=net-10.0-pp)

<details>
<summary>💡 Show Method Signature</summary>

```csharp
public async ValueTask<EvaluationResult> EvaluateAsync(
    IEnumerable<ChatMessage> messages,
    ChatResponse modelResponse,
    ChatConfiguration? chatConfiguration = null,
    IEnumerable<EvaluationContext>? additionalContext = null,
    CancellationToken cancellationToken = default)
{
    ArgumentNullException.ThrowIfNull(modelResponse);
    ArgumentNullException.ThrowIfNull(chatConfiguration);

    var numericMetric = new NumericMetric(MetricName);
    var result = new EvaluationResult(numericMetric);

    // Validation and evaluation logic will be added next...
}
```

</details>

## Add Input Validation

1. [ ] Extract the user request from messages using the `TryGetUserRequest()` extension method
1. [ ] If extraction fails, add an error diagnostic and return early
1. [ ] Validate the model response text is not null or whitespace
1. [ ] If validation fails, add an error diagnostic and return early

> [!knowledge] Defensive Evaluation
> 
> Robust evaluators validate their inputs and provide clear error diagnostics when something is wrong. This helps with debugging test failures.

<details>
<summary>💡 Show Validation Logic</summary>

```csharp
if (!messages.TryGetUserRequest(out ChatMessage? userRequest, out IReadOnlyList<ChatMessage> conversationHistory))
{
    result.AddDiagnosticsToAllMetrics(
        EvaluationDiagnostic.Error(
            $"The ${messages} supplied for evaluation did not contain a user request as the last message."));
    return result;
}

if (string.IsNullOrWhiteSpace(modelResponse.Text))
{
    result.AddDiagnosticsToAllMetrics(
        EvaluationDiagnostic.Error($"The {nameof(modelResponse)} supplied for evaluation was null or empty."));
    return result;
}
```

</details>

## Create the Evaluation Prompt Method

1. [ ] Create a `private static` method named `GetEvaluationInstructions` that returns `List<ChatMessage>`
1. [ ] Accept parameters for: `userRequest`, `modelResponse`, `includedHistory`, and `additionalContext`
1. [ ] Render the model response and user request as text
1. [ ] Extract the expected answer from the `Context` in `additionalContext`
1. [ ] If the context is missing, throw an `InvalidOperationException`

<details>
<summary>💡 Show Method Start</summary>

```csharp
private static List<ChatMessage> GetEvaluationInstructions(
    ChatMessage? userRequest,
    ChatResponse modelResponse,
    IEnumerable<ChatMessage> includedHistory,
    IEnumerable<EvaluationContext>? additionalContext)
{
    string renderedModelResponse = modelResponse.RenderText();
    string renderedUserRequest = userRequest?.RenderText() ?? string.Empty;
    string answer;

    if (additionalContext is not null &&
        additionalContext.OfType<Context>().FirstOrDefault() is Context context)
    {
        answer = context.ExpectedAnswer;
    }
    else
    {
        throw new InvalidOperationException($"The ExpectedAnswer must be provided in the additional context.");
    }

    // Build the prompt next...
}
```

</details>

## Build the LLM-as-Judge Prompt

1. [ ] Create a prompt that:
   - Describes the AI assistant's domain (Xbox Gamepass library)
   - Presents the question, expected truth, and assistant's answer
   - Defines the JSON output format with scores and descriptions
   - Specifies scoring criteria (1-5 scale)
1. [ ] Return the prompt wrapped in a `ChatMessage` with `ChatRole.User`

> [!knowledge] LLM-as-Judge Pattern
> 
> This pattern uses another LLM to evaluate responses. The key is crafting a clear prompt that defines:
> - The evaluation criteria
> - The expected output format
> - The scoring scale and what each score means

<details>
<summary>💡 Show Complete Prompt</summary>

```csharp
var prompt = $$"""
There is an AI assistant that answers questions about games in the Xbox Gamepass library. The questions
may relate to game completion rate, playtime, rating, and achievement score details.

You are evaluating the quality of an AI assistant's response to several questions. Here are the
questions, the desired true answers, and the answers given by the AI system:

<questions>
    <question index="0">
        <text>{{renderedUserRequest}}</text>
        <truth>{{answer}}</truth>
        <assistantAnswer>{{renderedModelResponse}}</assistantAnswer>
    </question>
</questions>

Evaluate each of the assistant's answers separately by replying in this JSON format:

{
    "scores": [
        { "index": 0, "descriptionOfQuality": string, "scoreLabel": number },
        { "index": 1, "descriptionOfQuality": string, "scoreLabel": number },
        ... etc ...
    ]
]

Score only based on whether the assistant's answer is true and answers the question. As long as the
answer covers the question and is consistent with the truth, it should score as perfect. There is
no penalty for giving extra on-topic information or advice. Only penalize for missing necessary facts
or being misleading, or providing incorrect information that has no basis in the knowledgebase.

The descriptionOfQuality should be up to 5 words summarizing to what extent the assistant answer
is correct and sufficient.

Based on descriptionOfQuality, the scoreLabel must be a number between 1 and 5 inclusive, where 5 is best and 1 is worst.
Do not use any other words for scoreLabel. You may only pick one of those scores.

"""
;

return [new ChatMessage(ChatRole.User, prompt)];
```

</details>

## Add Response Record Types

1. [ ] Outside the `AnswerScoringEvaluator` class (but in the same file), add record types for structured output parsing:

```csharp
record ScoringResponse(AnswerScore[] Scores);
record AnswerScore(int Index, int ScoreLabel, string DescriptionOfQuality);
```

> [!knowledge] Structured Output
> 
> Using records with `GetResponseAsync<T>()` enables the LLM to return structured data that can be easily parsed and processed.

## Complete the Evaluation Logic

1. [ ] In `EvaluateAsync`, call `GetEvaluationInstructions()` to build the prompt
1. [ ] Use `chatConfiguration.ChatClient.GetResponseAsync<ScoringResponse>()` to get structured output
1. [ ] Handle cases where the response is invalid or contains no scores
1. [ ] Extract the score and set `numericMetric.Value`
1. [ ] Add the quality description as an informational diagnostic

<details>
<summary>💡 Show Evaluation Logic</summary>

```csharp
var evaluationInstructions = GetEvaluationInstructions(
    userRequest,
    modelResponse,
    conversationHistory,
    additionalContext);

var response = await chatConfiguration.ChatClient.GetResponseAsync<ScoringResponse>(
    evaluationInstructions,
    cancellationToken: cancellationToken);

if (!response.TryGetResult(out var scoringResponse))
{
    result.AddDiagnosticsToAllMetrics(
        EvaluationDiagnostic.Error("Scoring response was not provided in a valid format."));
    return result;
}

if (scoringResponse.Scores is not [var score, ..])
{
    result.AddDiagnosticsToAllMetrics(
        EvaluationDiagnostic.Error("Scoring response contained no scores."));
    return result;
}

numericMetric.Value = score.ScoreLabel;

if (!string.IsNullOrWhiteSpace(score.DescriptionOfQuality))
{
    numericMetric.AddDiagnostics(EvaluationDiagnostic.Informational(score.DescriptionOfQuality));
}
```

</details>

## Add the Interpret Helper Method

1. [ ] Create an `internal static` method named `Interpret` that takes a `NumericMetric` and returns `EvaluationMetricInterpretation`
1. [ ] Map scores 1-5 to appropriate `EvaluationRating` values:
   - 1 → `Unacceptable`
   - 2 → `Poor`
   - 3 → `Average`
   - 4 → `Good`
   - 5 → `Exceptional`
1. [ ] Return `Inconclusive` for unexpected scores and mark as failed

<details>
<summary>💡 Show Interpret Implementation</summary>

```csharp
internal static EvaluationMetricInterpretation Interpret(NumericMetric metric)
{
    double score = metric?.Value ?? -1.0;
    EvaluationRating rating = score switch {
        1.0 => EvaluationRating.Unacceptable,
        2.0 => EvaluationRating.Poor,
        3.0 => EvaluationRating.Average,
        4.0 => EvaluationRating.Good,
        5.0 => EvaluationRating.Exceptional,
        _ => EvaluationRating.Inconclusive,
    };
    return new EvaluationMetricInterpretation(rating, failed: rating == EvaluationRating.Inconclusive);
}
```

</details>

## Set the Interpretation and Return

1. [ ] At the end of `EvaluateAsync`, call `Interpret()` and assign to `numericMetric.Interpretation`
1. [ ] Return the result

```csharp
numericMetric.Interpretation = Interpret(numericMetric);
return result;
```

---

# Part 2: Integrate the Custom Evaluator into Tests

## Update the GetEvaluators Method

1. [ ] Open your **AgentRetrievalEvalTests.cs** file from US2
1. [ ] Add the `AnswerScoringEvaluator` to your evaluators collection

<details>
<summary>💡 Show Updated GetEvaluators</summary>

```csharp
private static IEnumerable<IEvaluator> GetEvaluators()
{
    var relevanceEvaluator = new RelevanceEvaluator();
    var coherenceEvaluator = new CoherenceEvaluator();
    var groundednessEvaluator = new GroundednessEvaluator();
    var answerScoringEvaluator = new AnswerScoringEvaluator();
    return [ relevanceEvaluator, coherenceEvaluator, groundednessEvaluator, answerScoringEvaluator ];
}
```

</details>

## Update the EvaluateQuestion Method

1. [ ] Add the `AnswerScoringEvaluator.Context` to the `additionalContext` array in `EvaluateAsync()`
1. [ ] Pass the expected answer from the `EvalQuestion` record

<details>
<summary>💡 Show Updated EvaluateAsync Call</summary>

```csharp
var result = await scenario.EvaluateAsync(
    messages: [new ChatMessage(ChatRole.User, question.Question)],
    modelResponse: response.ToChatResponse(),
    additionalContext: [new AnswerScoringEvaluator.Context(question.Answer),
        new GroundednessEvaluatorContext(await GetKnowledgebaseContext())],
    cancellationToken: cancellationToken
);
```

</details>

## Update the Validate Method

1. [ ] Add assertions for the new `AnswerScoringEvaluator` metric
1. [ ] Retrieve the score using `AnswerScoringEvaluator.AnswerScoreMetricName`
1. [ ] Assert the rating is `Good` or `Exceptional` and not failed

<details>
<summary>💡 Show Updated Validate Method</summary>

```csharp
private static void Validate(EvaluationResult result)
{
    // Retrieve the score for relevance from the EvaluationResult.
    NumericMetric relevance =
        result.Get<NumericMetric>(RelevanceEvaluator.RelevanceMetricName);
    Assert.IsFalse(relevance.Interpretation?.Failed, relevance.Reason);
    Assert.IsTrue(relevance.Interpretation?.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);

    // Retrieve the score for coherence from the EvaluationResult.
    NumericMetric coherence =
        result.Get<NumericMetric>(CoherenceEvaluator.CoherenceMetricName);
    Assert.IsFalse(coherence.Interpretation?.Failed, coherence.Reason);
    Assert.IsTrue(coherence.Interpretation?.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);
            
    // Retrieve the score for groundedness from the EvaluationResult.
    NumericMetric groundedness =
        result.Get<NumericMetric>(GroundednessEvaluator.GroundednessMetricName);
    Assert.IsFalse(groundedness.Interpretation?.Failed, groundedness.Reason);
    Assert.IsTrue(groundedness.Interpretation?.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);
    
    // Retrieve the score for answer quality from the EvaluationResult.
    NumericMetric answerScore = result.Get<NumericMetric>(AnswerScoringEvaluator.AnswerScoreMetricName);
    Assert.IsTrue(answerScore.Interpretation?.Rating is EvaluationRating.Good or EvaluationRating.Exceptional);
    Assert.IsFalse(answerScore.Interpretation?.Failed, answerScore.Reason);
}
```

</details>

---

## Run and Validate

1. [ ] Build the solution to verify there are no compilation errors
1. [ ] Execute the test using the `dotnet test` CLI:

	```Powershell
	dotnet test tests/AgentEvalsWorkshop.Tests --filter "FullyQualifiedName~AgentRetrievalEvalTests"
	```

	@[Click this to run the unit tests][Run Unit Tests]{Powershell}

[Run Unit Tests]:
```Powershell
cd d:\github\seiggy\agent-unit-testing
dotnet test tests/AgentEvalsWorkshop.Tests --filter "FullyQualifiedName~AgentRetrievalEvalTests"
```

## Generate and View the Evaluation Report

1. [ ] After the tests complete, generate an HTML report:

	```Powershell
	dotnet aieval report -p C:\TestReports -o custom-eval-report.html
	```

	@[Click this to run the test report][AIEval Report]{Powershell}

[AIEval Report]:
```Powershell
cd d:\github\seiggy\agent-unit-testing
dotnet aieval report -p C:\TestReports -o custom-eval-report.html
```

2. [ ] Open `custom-eval-report.html` in your browser

**What you'll see in the report:**

- **Answer Score Metric**: Your custom evaluator's scores alongside the built-in evaluators
- **Quality Descriptions**: The 5-word summaries from your evaluator's diagnostics
- **Four Metrics Per Question**: Relevance, Coherence, Groundedness, and Answer Score

📚 **Documentation Links:**
- [aieval report tool](https://learn.microsoft.com/en-us/dotnet/ai/evaluation/evaluate-with-reporting#generate-a-report)

---

## Success Criteria

A passing test suite indicates:
- ✅ Your custom evaluator correctly implements the `IEvaluator` interface
- ✅ The evaluator receives and uses the expected answer context
- ✅ LLM-as-Judge scoring produces meaningful results
- ✅ Scores are correctly mapped to `EvaluationRating` values
- ✅ All four evaluators work together in the test suite
- ✅ All ratings are `Good` or `Exceptional`

---

## Troubleshooting

| Symptom | Possible Cause | Resolution |
|---------|----------------|------------|
| `InvalidOperationException` for ExpectedAnswer | Missing context | Verify `AnswerScoringEvaluator.Context` is passed in `additionalContext` |
| `TryGetResult` returns false | LLM response doesn't match schema | Check that the prompt clearly specifies the JSON format |
| Score is always `Inconclusive` | Score is outside 1-5 range | Review the LLM's raw response for unexpected values |
| Answer Score is low but response looks correct | Expected answer too specific | Make expected answers focus on key facts, not exact wording |
| `NullReferenceException` on metric | Evaluator not registered | Ensure `AnswerScoringEvaluator` is in `GetEvaluators()` return |

---

## Extension Challenges

Once you've completed the exercise, try these extensions:

1. **Add Detailed Diagnostics**: Include the full reasoning from the LLM in the diagnostic output

2. **Partial Credit Scoring**: Modify the prompt to give partial credit for partially correct answers

3. **Multi-Aspect Scoring**: Create an evaluator that scores multiple aspects (accuracy, completeness, formatting) separately

4. **Confidence Scoring**: Have the LLM also report a confidence level for its score

5. **Custom Thresholds**: Add constructor parameters to customize what score constitutes `Good` vs `Exceptional`