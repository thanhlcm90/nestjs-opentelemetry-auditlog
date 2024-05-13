<p align="center">
<a href="https://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo-small.svg" width="120" alt="Nest Logo" /></a>
</p>
<h1 align="center">NestJS AuditLog</h1>

<p align="center">
  SDK for NestJS framework to implement audit log feature via many exporter.
  <p align="center">
    <a href="https://www.npmjs.com/package/nestjs-auditlog" target="_blank"><img alt="npm version" src="https://img.shields.io/npm/v/nestjs-auditlog" /></a>
    <a href="https://www.npmjs.com/package/nestjs-auditlog" target="_blank"><img alt="NPM" src="https://img.shields.io/npm/l/nestjs-auditlog" /></a>
    <a href="https://www.npmjs.com/package/nestjs-auditlog" target="_blank"><img alt="npm downloads" src="https://img.shields.io/npm/dm/nestjs-auditlog" /></a>
     <a href="https://coveralls.io/github/thanhlcm90/nestjs-auditlog?branch=main" target="_blank"><img alt="coverage" src="https://coveralls.io/repos/github/thanhlcm90/nestjs-auditlog/badge.svg?branch=main" /></a>
  </p>
</p>

## Table of Contents

- [Description](#description)
- [Installation](#installation)
- [Example](#example)
- [Configuration](#configuration)
- [Contact and Feedback](#contact-and-feedback)
- [License](#license)

## Description

Audit logging is the process of documenting activity within the software systems used across your organization. Audit logs record the occurrence of an event, the time at which it occurred, the responsible user or service, and the impacted entity. All of the devices in your network, your cloud services, and your applications emit logs that may be used for auditing purposes.

## Installation

You can install the library using npm:

```
npm install nestjs-auditlog
```

## Example

To integrate `nestjs-auditlog` into your NestJS application, follow these steps:

1. First, import the module with `AuditLogModule.forRoot(...)` or `AuditLogModule.forRootAsync(...)` into your root `AppModule`. (refer to the module configuration documentation [below](#configuration)).

```ts
import { AuditLogModule } from 'nestjs-auditlog';

@Module({
  imports: [AuditLogModule.forRoot({
    exporter: new OpenTelemetryHttpExporter('service1', 'user', '127.0.0.1:4318')
  })],
  ...
})
class AppModule {}
```

2. Next, put the decorator `@AuditLog` to every api you want to send audit log.

```typescript
import { Controller, Get, Query, Body, Post } from '@nestjs/common';
import { AuditLog } from 'nestjs-auditlog';

@Controller('/')
export class CatsController {
  @AuditLog({
    resource_type: 'Cat',
    resource_id_field_map: 'query.id',
    operator_id: 'findTheCat',
    operator_type: 'Query',
  })
  @Get()
  findTheCat(@Query('id') id: string): any {
    return `Congratulations! You have found the cat ${id}!`;
  }

  @AuditLog({
    resource_type: 'Cat',
    resource_id_field_map: 'body.id',
    operator_id: 'createTheCat',
    operator_type: 'Create',
  })
  @Post()
  createTheCat(@Body() body: any): any {
    return `Congratulations! You created the cat ${body.id}!`;
  }
}
```

Please note that the above code snippets demonstrate the basic setup of `nestjs-auditlog` in your NestJS application. Make sure to adjust the code based on your specific application requirements and configuration.

## Configuration

### Configuration interface

The following interface is used for `AuditLogModule` configuration:

```ts
export interface IAuditLogConfigOptions {
  /**
   * setup audit log exporter
   */
  exporter: IAuditLogExporter;
}
```

### Zero configuration

Just import `AuditLogModule` to `AppModule`:

```ts
import { AuditLogModule } from 'nestjs-auditlog';

@Module({
  imports: [AuditLogModule.forRoot()],
  ...
})
class AppModule {}
```

### Asynchronous configuration

With `AuditLogModule.forRootAsync` you can, for example, import your `ConfigModule` and inject `ConfigService` to use it in `useFactory` method.

`useFactory` should return object with [Configuration interface](#configuration-interface)

Here's an example:

```ts
import { AuditLogModule } from 'nestjs-auditlog';

@Injectable()
class ConfigService {
  public readonly timeout = 10000;
}

@Module({
  providers: [ConfigService],
  exports: [ConfigService]
})
class ConfigModule {}

@Module({
  imports: [
    AuditLogModule.forRootAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: async (config: ConfigService) => {
        await somePromise();
        return {
          exporter: new AuditLoggerDefaultExporter(),
        };
      }
    })
  ],
  ...
})
class AppModule {}
```

## Contact and Feedback

If you have any ideas, comments, or questions, don't hesitate to contact me

Best regards,

Daniel Le

## License

This library is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.
