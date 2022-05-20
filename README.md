# stackwriter-serverless

![license](https://img.shields.io/badge/License-Apache_2.0-blue.svg)
![maven-version](https://img.shields.io/maven-central/v/net.sozal/stackwriter)
![layer-version](https://api.globadge.com/v1/badgen/aws/lambda/layer/latest-version/us-east-1/273094347961/stackwriter) (badge powered by [Globadge serverless](https://www.globadge.com/badges/serverless))

`StackWriter` allows developers to be able to get, manipulate and send/report callstacks (frames in the stacktraces)
with local variables (name, type and value) manually (by programmatic API) or automatically (on error).

## Supported Environments

| Architecture/Runtime | `java8`            |  `java8.al2`       | `java11`           |
|:---------------------|:------------------:|:------------------:|:------------------:|
| **`x86_64`**         | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| **`arm64`**          | :x:                |     :x:            |  :x:               |  

## Installation

`StackWriter` serverless has 2 components (Agent and API) need to be installed:

- Add agent as layer into your AWS Lambda function:
```
arn:aws:lambda:${region}:273094347961:layer:stackwriter:${layer-version}

```

You can use the version shown below instead of `$layer-version}`:

![layer-version](https://api.globadge.com/v1/badgen/aws/lambda/layer/latest-version/us-east-1/273094347961/stackwriter) (badge powered by [Globadge serverless](https://www.globadge.com/badges/serverless))

- Add API as `provided` (no need to bundle it with the artifact as it is also available on AWS Lambda environment through by layer) dependency into your project:
```
<dependency>
    <groupId>net.sozal</groupId>
    <artifactId>stackwriter-api</artifactId>
    <version>${stackwriter.version}</version>
    <scope>provided</scope>
</dependency>
```

You can use the version shown below instead of ${stackwriter.version}.

![maven-version](https://img.shields.io/maven-central/v/net.sozal/stackwriter)

- Configure AWS Lambda function to use agent:

### `java8` (Java 8 on Amazon Linux) Runtime
- Set runtime to `Custom runtime`.

**Note:** `Custom runtime on Amazon Linux 2` is not supported yet.

### `java8.al2` (Java 8 on Amazon Linux 2) Runtime
- Set `AWS_LAMBDA_EXEC_WRAPPER` environment variable to `/opt/stackwriter_wrapper`.

### `java11` (Java 11 on Amazon Linux 2) Runtime
- Set `AWS_LAMBDA_EXEC_WRAPPER` environment variable to `/opt/stackwriter_wrapper`.

## Usage

### Getting callstack
```java
import net.sozal.stackwriter.api.StackWriter;
import net.sozal.stackwriter.api.StackWriter;
import net.sozal.stackwriter.api.domain.Frame;

...

        System.out.println("FRAMES:");
        for (Frame frame : StackWriter.takeSnapshot()) {
        System.out.println(String.format(
        "  - %s#%s@%d",
        frame.getClazz().getName(),
        frame.getMethod().getName(),
        frame.getLine()));
        Map<String, LocalVariable> locals = frame.getLocals();
        for (LocalVariable local : locals.values()) {
        String localVarName = local.getName();
        Class localVarType = local.getType();
        Object localVarValue = local.getValue();
        System.out.println(String.format(
        "    + %s: type=%s, value=%s",
        localVarName, localVarType, localVarValue));
        }
        }

```

## Roadmap

- Automatically collect callstack on error (all errors or specified errors)
- `arm64` support
- Ability to send collected callstack to remote services (Webhook, Elasticsearch, Prometheus, etc ...)

## Contributing

Everyone is very welcome to contribute to this repository.
Feel free to [raise issues](https://github.com/serkan-ozal/stackwriter-serverless/issues)
or to [submit Pull Requests](https://github.com/serkan-ozal/stackwriter-serverless/pulls).

## License

Licensed under [Apache License 2.0](LICENSE).
