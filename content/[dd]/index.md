---
emoji: ๐ฎ
title: ์์ธ ์ฒ๋ฆฌ(Exception filters)
date: '2022-01-11 00:00:00'
author: ์ค์ฝ๋ฉ
tags: ๋ธ๋ก๊ทธ github-pages gatsby
categories: ๋ธ๋ก๊ทธ featured
---

# ์์ธ ์ฒ๋ฆฌ

Nest์๋ ๋ชจ๋  ์์ธ๋ฅผ ์ฒ๋ฆฌํ๋ ์์ฒด ์์ธ ๋ ์ด์ด๋ฅผ ๊ฐ์ง๊ณ  ์๋ค. ์ ํ๋ฆฌ์ผ์ด์ ์ฝ๋์์ ์์ธ ์ฒ๋ฆฌ๋ฅผ ํ์ง ์์๋ ์์์ ๋ณด๊ธฐ ์ฌ์ด ํ์์ผ๋ก ์๋ฌ๋ฅผ ๋ณํํ์ฌ ์ ์กํ๋ค.

<br>

### ๐ผ ํ์ค ์์ธ ์ฒ๋ฆฌ

๊ฐ๊ฐ 400๊ณผ 500์ ์๋ฌ ์ฝ๋๋ฅผ Nest๊ฐ ๋ณด๋ธ ๊ฒฐ๊ณผ๋ ์๋์ ๊ฐ๋ค.

```
{
  "statusCode": 404,
  "message": "Cannot Get /dfesd",
  "error": "Not Found"
}
```

```
{
  "statusCode": 500,
  "message": "Internal server error"
}
```

๊ธฐ๋ณธ์ ์ผ๋ก ์ด ์์์ ๋ด์ฅ๋ ์ ์ญ ์์ธ ํํฐ์ ์ํด ์ํ๋๋ค. ์ ์ญ ์์ธ ํํฐ๋ `HttpException`์ ์์ธ๋ฅผ ๋ค๋ฃจ๋ฉฐ, ์ธ์ํ  ์ ์๋ ์๋ฌ(HttpException๋ ์๋๊ณ , HttpException๋ฅผ ์์๋ฐ์ง๋ ์์ ์๋ฌ)์ ๊ฒฝ์ฐ ๋ด์ฅ ์์ธ ํํฐ๊ฐ ์์ ๊ฐ์ ๊ธฐ๋ณธ JSON ์๋ต์ ์์ฑํ๋ค.

๋ง์ฝ ์๋์ ๊ฐ์ ๋ผ์ฐํธ ํธ๋ค๋ฌ์์ ์์ธ๋ฅผ ๋์ง๋ฉด ์๋ฌ๋ฅผ ๋ค์์ฒ๋ผ ์๋ตํ๋ค.

```
  @Get()
  getAllCat() {
    throw new HttpException('api is broken', 401);
    return 'all cat';
  }
```

```
{
  "statusCode": 401,
  "message": "api is broken"
}
```

`statusCode`์ `message`๊ฐ ๊ฐ์ ๋์ด์ ์ถ๋ ฅ๋๋ ๊ฒ์ ํ์ธํ  ์ ์๋ค. Nest๊ฐ ์ ๊ณตํ๋ ๋๋ก ์์ธ๋ฅผ ์ฒ๋ฆฌํ  ์๋ ์์ง๋ง ์๋น์ค์ ํํ์ ๋ฐ๋ผ ์๋ฌ๋ฅผ ํธ๋ค๋งํ  ๋ ์ปค์คํ์ ํ  ์๋ ์๋ค.

<br>

- `statusCode` : `status`์ธ์์ ์ ๊ณต๋ HTTP ์ํ ์ฝ๋
- `message` : ํด๋น HTTP ์ค๋ฅ์ ๋ํ ๊ฐ๋ตํ ์ค๋ช

๊ธฐ๋ณธ์ ์ผ๋ก JSON ์๋ต ๋ณธ๋ฌธ์๋ ์์ ๋ ๊ฐ์ง ์์ฑ์ด ํฌํจ๋๋ค. JSON ์๋ต ๋ณธ๋ฌธ์ ๋ฉ์์ง ๋ถ๋ถ์ ์ฌ์ ์ํ๋ ค๋ฉด ์๋ต ์ธ์์ ๋ฌธ์์ด์ ์๋ ฅํ๋ฉด ๋๋ค. ์ ์ฒด JSON ์๋ต ๋ณธ๋ฌธ์ ์ฌ์ ์ํ๊ณ  ์ถ๋ค๋ฉด ์๋์ ์ฝ๋์ฒ๋ผ ์๋ต ์ธ์์ ๊ฐ์ฒด๋ฅผ ์ ๋ฌํ๋ค. Nest๋ ์ ๋ฌ๋ฐ์ ๊ฐ์ฒด๋ฅผ ์ง๋ ฌํํ๊ณ  JSON ์๋ต ๋ณธ๋ฌธ์ผ๋ก ๋ฐํํ๋ค.

```
  @Get()
  getAllCat() {
    throw new HttpException({ success: false, message: 'api is broken!' });
    return 'all cat';
  }
```

```
{
  "success": false,
  "message": "api is broken!"
}
```

์ด๋ ๊ฒ ์ํ๋ ํ์์ผ๋ก ๋ฐ๊ฟ ์ปค์คํ์ ํ๋ฉด ์ค๋ฒ๋ผ์ด๋ฉ๋์ด ์ถ๋ ฅ์ด ๋๋ค.

<br>

### ๐ผ ์์ธ ํํฐ

์์ธ ์ฒ๋ฆฌ์ ์ฌ์ฌ์ฉ์ฑ์ ๊ณ ๋ คํ์ฌ ํ๋๋ก ๋ชจ์ฌ ํํฐ๋ง์ ๊ฑธ์น ํ response๋ก ๋ฐํํด ์ฃผ๋ ํ์์ผ๋ก ์์ธ ํํฐ๋ฅผ ๋ง๋ค ์ ์๋ค. ์์ธ ํํฐ๋ฅผ ๋ง๋ค๊ธฐ ์ํด ๊ณต์ ํํ์ด์ง๋ฅผ ์ฐธ๊ณ ํ์ฌ `http-exceiption.filter.ts`ํ์ผ์ ์์ฑํ๋ค.

```
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
} from '@nestjs/common';
import { Request, Response } from 'express';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
    });
  }
}
```

์ด๋ ๊ฒ ์์ฑ๋ ํํฐ๋ฅผ ์ ์ฉํ๋ ๋ฐฉ๋ฒ์๋ ๋ ๊ฐ์ง๊ฐ ์๋ค. **(1)** `@UseFilter()` ๋ฐ์ฝ๋ ์ดํฐ๋ก controller์ ๊ฐ๊ฐ ์ ์ฉํ๋ ๋ฐฉ๋ฒ **(2)** ์ ์ญ์์ ์ ์ฉํ๋ ๋ฐฉ๋ฒ์ด ์๋๋ฐ ๋ณดํต ์ ์ญ ํํฐ ํ๋๋ง ๊ฐ์ง๋ ๊ฒ ๋ ์ผ๋ฐ์ ์ธ ๋ฐฉ๋ฒ์ด๋ผ๊ณ  ํ๋ค.

#### (1) controller์ ๊ฐ๊ฐ ์ ์ฉํ๋ ๋ฐฉ์

```
  @Get()
  @UseFilters(HttpExceptionFilter)
  getAllCat() {
    throw new HttpException('api is broken', 401);
    return 'all cat';
  }
```

`@UseFilters(HttpExceptionFilter)`๋ฅผ ๋ฐ์ฝ๋ ์ดํฐ๋ก ๋ฃ์ผ๋ฉด `throw new ~`์์ ๋ฐ์๋ ์๋ฌ๊ฐ `HttpExceptionFilter`์์ ํํฐ๋ง๋์ด response์ ์ ๋ฌ๋๋ค.

```
{
   "statusCode":401,
   "timestamp":"2022-01-06T02:54:26.555Z",
   "path":"/cats"
}
```

์ด์ ๊ณผ๋ ์์ธ ์ฒ๋ฆฌ์ JSON๊ณผ๋ ์กฐ๊ธ ๋ค๋ฅด๊ฒ ์ฐํ ๊ฒ์ ํ์ธํ  ์ ์๋ค. ์ด๋ ๊ฒ ๋ํ๋ ์ด์ ๋ ๋ฐ๋ก `HttpExceptionFilter`์์ ํํฐ๋ง์ ๊ฑฐ์ณค๊ธฐ ๋๋ฌธ์ด๋ค. ํด๋น ํด๋์ค๋ฅผ ๋ณด๋ฉด `response.status(status).json`์ผ๋ก ์ ์ธ ๊ฐ์ง ์์ฑ์ ๋ฐ๋๋ค. ์ฆ, ์ฌ๊ธฐ์ ๋ณ๊ฒฝํ๋ ๊ฒ์ผ๋ก ์ปค์คํ์ด ๊ฐ๋ฅํ๋ค.

<br>

'api is broken' ๋ฉ์ธ์ง๋ฅผ ์์ธ ํํฐ์ ํฌํจ์ํค๋ ค๋ฉด `exception.getResponse()` ๋ฉ์๋๋ฅผ ์ด์ฉํ  ์ ์๋ค. 'api is broken' ์ธ์๊ฐ getResponse๋ก ์ ๋ฌ๋์ด ์๋ฌ ๋ฉ์์ง๊ฐ ์ฐํ๋ค.

```
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
} from '@nestjs/common';
import { Request, Response } from 'express';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();
    const error = exception.getResponse();

    response.status(status).json({
      success: false,
      timestamp: new Date().toISOString(),
      path: request.url,
      error,
    });
  }
}
```

error๋ key์ value๊ฐ ๋๊ฐ๊ธฐ ๋๋ฌธ์ ์๋ตํ๋ค. ์ด ์ํ์์ ์์ฒญ์ ๋ณด๋ด๋ฉด ์๋์ฒ๋ผ ์๋ฌ ์ฝ๋๊ฐ ๋ฐ์ํ๋ค.

```
{
   "success":false,
   "timestamp":"2022-01-06T03:06:04.940Z",
   "path":"/cats",
   "error":"api is broken"
}
```

์ด์ฒ๋ผ controller ๋ด๋ถ ๋ผ์ฐํฐ ๊ฐ๊ฐ์ `@UseFilters(HttpExceptionFilter)`๋ฅผ ๋ฐ์ฝ๋ ์ดํฐ๋ก ๋ฃ์ ์๋ ์๊ณ  ์๋์ฒ๋ผ `@Controller` ๋ฐ์ฝ๋ ์ดํฐ ๋ฐ๋ก ๋ฐ์ ๋ฑ ํ๋๋ง ๋ฃ์ด ์ค ์๋ ์๋ค.

```
@Controller('cats')
@UseFilters(HttpExceptionFilter)
export class CatsController {
  constructor(private readonly catsService: CatsService) {}

...
```

์คํ ๋ด์ฉ์ ๋น์ฐํ ๋์ด ๋์ผํ๋ค.

<br>

#### (2) ์ ์ญ์ผ๋ก ์ ์ฉํ๋ ๋ฐฉ์

`http-exceiption.filter.ts` ํ์ผ์ `HttpExceptionFilter` ํด๋์ค๋ฅผ ๋ง๋๋ ๊ฒ๊น์ง๋ ๋์ผํ๋ค.

```
import { HttpExceptionFilter } from 'src/http-exception.filter';
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new HttpExceptionFilter());  // ์ ์ญ ํํฐ ์ ์ฉ
  await app.listen(3000);
}
bootstrap();
```

`useGlobalFilters`๋ฅผ ์ถ๊ฐํด ์ฃผ๋ฉด ๋๋ค. 404 ์๋ฌ๋ ์๋์ ๊ฐ์ด ๋ฌ๋ค.

```
{
  "success":false,
  "timestamp": "2021-10-04T09:53:19.086Z",
  "path": "/cats",
  "error": {
    "statusCode": 404,
    "message": "Cannot GET /cat324234",
    "error": "api is broken"
  }
}
```
