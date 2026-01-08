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
> The terminal will display a URL for the Aspire Dashboard (typically ++https://localhost:15041++ or similar). Open this URL in your browser.

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
