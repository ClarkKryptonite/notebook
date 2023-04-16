# Retrofit 2.9.0 源码解析

## Retrofit 配置

1. 设置 BASE_URL
2. 设置 Client->一般是 OkHttpClient
3. 设置 CallAdapterFactory，如 RxJava3CallAdapterFactory
4. 设置 ConverterFactory，如 GsonConverterFactory

## 执行 retrofit.create(Class<?>)

1. 首先检验 ApiService Interface 的合法性，确保没有泛型参数
2. 如果配置 eager 参数，会直接为每个 ApiService 中的方法生成对应的 ServiceMethod
3. 通过 Proxy.newInstance 生成具体的 ApiService 实例

## 执行 ApiService 中具体的方法

1. 判断 method 所在的 Class 是否是 Object.class, 如果是则直接 invoke 该方法
2. 判断是不是 default 方法，如果是直接执行, 否则接着往下走
3. 如果内存中已经存在了 ServiceMethod，直接返回并调用 invoke 方法
4. 解析 method 中标记 Method 的 Annotation 以及，方法参数中的 Annotation，校验，解析对应的参数并保存下来
5. 判断 method 的返回值是不是具体的类型，如果是未实例的泛型类型或通配符类型直接报错
6. 接下来获取对应的 callAdapter 以及 Converter,
7. callAdapter 将 OkHttpCall<ResponseType>转化成 Method 实际返回对象, 如将 retrofit2.Call<ResponseBody>转化为 Observable<ResponseBody>,
8. converter 负责将 http 的信息转化为对应的实例, 一般是将 ResponseBody 转化为其他实例
9. 生成对应的 HttpServiceMethod 对象，并调用 invoke 方法
10. invoke 方法中会生成 okhttpcall 并传入 adapter()方法，最后生成具体的请求对象。

## 请求对象调用执行方法后

数据返回后，Converter 会将返回的 ResponseBody 转化为对应的对应的对象
