---
emoji: 🔮
title: 예외 처리(Exception filters)
date: '2022-01-11 00:00:00'
author: 줌코딩
tags: 블로그 github-pages gatsby
categories: 블로그 featured
---

# 예외 처리

Nest에는 모든 예외를 처리하는 자체 예외 레이어를 가지고 있다. 애플리케이션 코드에서 예외 처리를 하지 않아도 알아서 보기 쉬운 형식으로 에러를 변환하여 전송한다.

<br>

### 🌼 표준 예외 처리

각각 400과 500의 에러 코드를 Nest가 보낸 결과는 아래와 같다.

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

기본적으로 이 작업은 내장된 전역 예외 필터에 의해 수행된다. 전역 예외 필터는 `HttpException`의 예외를 다루며, 인식할 수 없는 에러(HttpException도 아니고, HttpException를 상속받지도 않은 에러)의 경우 내장 예외 필터가 위와 같은 기본 JSON 응답을 생성한다.

만약 아래와 같은 라우트 핸들러에서 예외를 던지면 에러를 다음처럼 응답한다.

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

`statusCode`와 `message`가 강제되어서 출력되는 것을 확인할 수 있다. Nest가 제공하는 대로 예외를 처리할 수도 있지만 서비스의 형태에 따라 에러를 핸들링할 때 커스텀을 할 수도 있다.

<br>

- `statusCode` : `status`인수에 제공된 HTTP 상태 코드
- `message` : 해당 HTTP 오류에 대한 간략한 설명

기본적으로 JSON 응답 본문에는 위의 두 가지 속성이 포함된다. JSON 응답 본문의 메시지 부분을 재정의하려면 응답 인수에 문자열을 입력하면 된다. 전체 JSON 응답 본문을 재정의하고 싶다면 아래의 코드처럼 응답 인수에 객체를 전달한다. Nest는 전달받은 객체를 직렬화하고 JSON 응답 본문으로 반환한다.

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

이렇게 원하는 형식으로 바꿔 커스텀을 하면 오버라이딩되어 출력이 된다.

<br>

### 🌼 예외 필터

예외 처리의 재사용성을 고려하여 하나로 모여 필터링을 걸친 후 response로 반환해 주는 형식으로 예외 필터를 만들 수 있다. 예외 필터를 만들기 위해 공식 홈페이지를 참고하여 `http-exceiption.filter.ts`파일을 생성했다.

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

이렇게 생성된 필터를 적용하는 방법에는 두 가지가 있다. **(1)** `@UseFilter()` 데코레이터로 controller에 각각 적용하는 방법 **(2)** 전역에서 적용하는 방법이 있는데 보통 전역 필터 하나만 가지는 게 더 일반적인 방법이라고 한다.

#### (1) controller에 각각 적용하는 방식

```
  @Get()
  @UseFilters(HttpExceptionFilter)
  getAllCat() {
    throw new HttpException('api is broken', 401);
    return 'all cat';
  }
```

`@UseFilters(HttpExceptionFilter)`를 데코레이터로 넣으면 `throw new ~`에서 발생된 에러가 `HttpExceptionFilter`에서 필터링되어 response에 전달된다.

```
{
   "statusCode":401,
   "timestamp":"2022-01-06T02:54:26.555Z",
   "path":"/cats"
}
```

이전과는 예외 처리의 JSON과는 조금 다르게 찍힌 것을 확인할 수 있다. 이렇게 나타난 이유는 바로 `HttpExceptionFilter`에서 필터링을 거쳤기 때문이다. 해당 클래스를 보면 `response.status(status).json`으로 위 세 가지 속성을 받는다. 즉, 여기서 변경하는 것으로 커스텀이 가능하다.

<br>

'api is broken' 메세지를 예외 필터에 포함시키려면 `exception.getResponse()` 메서드를 이용할 수 있다. 'api is broken' 인자가 getResponse로 전달되어 에러 메시지가 찍힌다.

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

error는 key와 value가 똑같기 때문에 생략했다. 이 상태에서 요청을 보내면 아래처럼 에러 코드가 발생한다.

```
{
   "success":false,
   "timestamp":"2022-01-06T03:06:04.940Z",
   "path":"/cats",
   "error":"api is broken"
}
```

이처럼 controller 내부 라우터 각각에 `@UseFilters(HttpExceptionFilter)`를 데코레이터로 넣을 수도 있고 아래처럼 `@Controller` 데코레이터 바로 밑에 딱 하나만 넣어 줄 수도 있다.

```
@Controller('cats')
@UseFilters(HttpExceptionFilter)
export class CatsController {
  constructor(private readonly catsService: CatsService) {}

...
```

실행 내용은 당연히 둘이 동일하다.

<br>

#### (2) 전역으로 적용하는 방식

`http-exceiption.filter.ts` 파일에 `HttpExceptionFilter` 클래스를 만드는 것까지는 동일하다.

```
import { HttpExceptionFilter } from 'src/http-exception.filter';
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new HttpExceptionFilter());  // 전역 필터 적용
  await app.listen(3000);
}
bootstrap();
```

`useGlobalFilters`를 추가해 주면 된다. 404 에러는 아래와 같이 뜬다.

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
