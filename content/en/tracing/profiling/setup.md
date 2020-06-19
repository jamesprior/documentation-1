
Profiling libraries are shipped within the following tracing language libraries. Select your language below to learn how to enable profiling for your application:

{{< tabs >}}
{{% tab "Java" %}}

The Datadog Profiler requires [Java Flight Recorder][1]. The Datadog Profiling library is supported in OpenJDK 11+, Oracle Java 11+, and Zulu Java 8+ (minor version 1.8.0_212+). All JVM-based languages, such as Scala, Groovy, Kotlin, Clojure, etc. are supported. To begin profiling applications:

1. Download `dd-java-agent.jar`, which contains the Java Agent class files, and add the `dd-trace-java` version to your `pom.xml` or equivalent:

    ```shell
    wget -O dd-java-agent.jar 'https://repository.sonatype.org/service/local/artifact/maven/redirect?r=central-proxy&g=com.datadoghq&a=dd-java-agent&v=LATEST'
    ```

     **Note**: Profiling is available in the `dd-java-agent.jar` library in versions 0.44+.

2. Update your service invocation to look like:

    ```text
    java -javaagent:dd-java-agent.jar -Ddd.profiling.enabled=true -Ddd.profiling.api-key-file=<API_KEY_FILE> -jar <YOUR_SERVICE>.jar <YOUR_SERVICE_FLAGS>
    ```

    **Note**: With `dd-java-agent.jar` library versions 0.48+, if your organization is on Datadog EU site, add `-Ddd.site=datadoghq.eu` or set `DD_SITE=datadoghq.eu` as environment variable.

3. After a minute or two, visualize your profiles on the [Datadog APM > Profiling page][2].

**Note**:

- The `-javaagent` needs to be run before the `-jar` file, adding it as a JVM option, not as an application argument. For more information, see the [Oracle documentation][3]:

    ```shell
    # Good:
    java -javaagent:dd-java-agent.jar ... -jar my-service.jar -more-flags
    # Bad:
    java -jar my-service.jar -javaagent:dd-java-agent.jar ...
    ```

- Because profiles are sent directly to Datadog without using the Datadog Agent, you must pass a valid [Datadog API key][4].

- For advanced setup of the profiler or to add tags like `service` or `version`, use environment variables to set the parameters:

| Environment variable                             | Type          | Description                                                                                      |
| ------------------------------------------------ | ------------- | ------------------------------------------------------------------------------------------------ |
| `DD_PROFILING_ENABLED`                           | Boolean       | Alternate for `-Ddd.profiling.enabled` argument. Set to `true` to enable profiling.               |
| `DD_SITE`                                        | String        | Destination site for your profiles (versions 0.48+). Valid options are `datadoghq.com` for Datadog US site (default), and `datadoghq.eu` for the Datadog EU site. |
| `DD_SERVICE`                                     | String        | The Datadog [service][5] name.     |
| `DD_ENV`                                         | String        | The Datadog [environment][6] name, for example `production`.|
| `DD_VERSI0ON`                                     | String        | The version of your application.                             |
| `DD_TAGS`                                        | String        | Tags to apply to an uploaded profile. Must be a list of `<key>:<value>` separated by commas such as: `layer:api, team:intake`.  |


[1]: https://docs.oracle.com/javacomponents/jmc-5-4/jfr-runtime-guide/about.htm
[2]: https://app.datadoghq.com/profiling
[3]: https://docs.oracle.com/javase/7/docs/technotes/tools/solaris/java.html
[4]: /account_management/api-app-keys/#api-keys
[5]: /tracing/visualization/#services
[6]: /tracing/guide/setting_primary_tags_to_scope/#environment
{{% /tab %}}

{{% tab "Python" %}}

The Datadog Profiler requires Python 2.7+. Memory profiling only works on Python 3.5+. To begin profiling applications:

1. Install `ddtrace` which contains both tracing and profiling:

    ```shell
    pip install ddtrace
    ```

     **Note**: Profiling is available in the `ddtrace` library for versions 0.36+.

2. To automatically profile your code, import `ddtrace.profile.auto`. After import, the profiler starts:

    ```python
    import ddtrace.profiling.auto
    ```

3. After a minute or two, visualize your profiles on the [Datadog APM > Profiling page][1].

**Note**:

- Alternatively, profile your service by running it with the wrapper `pyddprofile`:

    ```shell
    $ pyddprofile server.py
    ```

- It is strongly recommended to add tags like `service` or `version` as it provides the ability to slice and dice your profiles across these dimensions, enhancing your overall product experience. Use environment variables to set the parameters:

| Environment variable                             | Type          | Description                                                                                      |
| ------------------------------------------------ | ------------- | ------------------------------------------------------------------------------------------------ |
| `DD_SITE`                                        | String        | If your organization is on Datadog EU site, set this to `datadoghq.eu`.                          |
| `DD_SERVICE`                                     | String        | The Datadog [service][2] name.     |
| `DD_ENV`                                         | String        | The Datadog [environment][3] name, for example `production`, which can be set here, or in `DD_PROFILING_TAGS` with `DD_PROFILING_TAGS="env:production"`. |
| `DD_VERSION`                                     | String        | The version of your application.                             |
| `DD_TAGS`                                        | String        | Tags to apply to an uploaded profile. Must be a list of `<key>:<value>` separated by commas such as: `layer:api, team:intake`.   |

<div class="alert alert-info">
Recommended for advanced usage only.
</div>

- If you want to manually control the lifecycle of the profiler, use the `ddtrace.profiling.profiler.Profiler` object:

    ```python
    from ddtrace.profiling import Profiler

    prof = Profiler()
    prof.start()

    # At shutdown
    prof.stop()
    ```

[1]: https://app.datadoghq.com/profiling
[2]: /tracing/visualization/#services
[3]: /tracing/guide/setting_primary_tags_to_scope/#environment
{{% /tab %}}

{{% tab "Go" %}}

The Datadog Profiler requires Go 1.12+. To begin profiling applications:

1. Get `dd-trace-go` using the command:

    ```shell
    go get gopkg.in/DataDog/dd-trace-go.v1
    ```

     **Note**: Profiling is available in the `dd-trace-go` library for versions 1.23.0+.

2. Import the [profiler][1] at the start of your application:

    ```Go
    import "gopkg.in/DataDog/dd-trace-go.v1/profiler"
    ```

3. Add the following snippet to start the profiler:

    ```Go
    err := profiler.Start()
    if err != nil {
        log.Fatal(err)
    }
    defer profiler.Stop()
    ```

4. After a minute or two, visualize your profiles in the [Datadog APM > Profiling page][2].

**Note**:

- It is strongly recommended to add tags like `service` or `version` as it provides the ability to slice and dice your profiles across these dimensions, enhancing your overall product experience. Use environment variables to set the parameters:

| Environment variable                             | Type          | Description                                                                                      |
| ------------------------------------------------ | ------------- | ------------------------------------------------------------------------------------------------ |
| `DD_SITE`                                        | String        | If your organization is on Datadog EU site, set this to `datadoghq.eu`.                          |
| `DD_SERVICE`                                     | String        | The Datadog [service][3] name.     |
| `DD_ENV`                                         | String        | The Datadog [environment][4] name, for example `production`.|
| `DD_VERSION`                                     | String        | The version of your application.                              |
| `DD_TAGS`                                        | String        | Tags to apply to an uploaded profile. Must be a list of `<key>:<value>` separated by commas such as: `layer:api, team:intake`.  |

[1]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/profiler#pkg-constants
[2]: https://app.datadoghq.com/profiling
[3]: /tracing/visualization/#services
[4]: /tracing/guide/setting_primary_tags_to_scope/#environment
{{% /tab %}}

{{< /tabs >}}
