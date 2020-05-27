# nestjs-mssql

[node-mssql](https://github.com/tediousjs/node-mssql) Connection Pool provider for [NestJS](https://nestjs.com).

Works as a NestJS module and injectable service that you can use to inject a local or global SQL connection pool into your NestJS modules or services.

## Installation

```sh
npm install @quickts/nestjs-mssql mssql
npm install -D @types/mssql
```

## How to use

### Example

Import and configure `MssqlModule` with the same configuration parameters accepted by `node-mssql`:

```ts
@Module({
    imports: [
        MssqlModule.forRoot({
            server: "10.0.0.1",
            user: "User",
            password: "Password",
            database: "DatabaseName",
        }),
    ],
    providers: [MyService],
    exports: [MyService],
    controllers: [MyController],
})
export class MyModule {}
```

Inject and the use `MssqlService` inside your services:

```ts
@Injectable()
export class MyService {
    constructor(private mssql: MssqlService) {}

    // Example service method with use of MssqlService:
    async getPartner(id: number) {
        const recordset = (
            await this.mssql.getPool().query(`SELECT PartnerId, Name FROM dbo.Partner WHERE PartnerId = '${id}' ORDER BY PartnerId;`)
        ).recordset;

        if (recordset.length) {
            return recordset[0];
        } else {
            throw new NotFoundException();
        }
    }
}
```

> **Note:** You should use prepared statements with `node-mssql` and not template strings to pass arguments to your queries, in order to prevent SQL injection attacks!
