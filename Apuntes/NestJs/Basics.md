# Controllers
Los controladores se encargan de llamar al servicio dependiendo de la ruta que se haya llamado, simplemente hace de intermedio entre la ruta y el servicio
Para los diferentes métodos que hay en las peticiones HTTP tenemos diferentes decoradores
```typescript
@Get() // Obtener algo de la BBDD
@Post() // Crear algo en la BBDD
@Put() // Actualizar un recurso completo de la BBDD
@Delete() // Eliminar algo de la BBDD
@Patch() // Actualizar algunos campos de la BBDD
@Options() // Se usa para preguntar que operaciones soporta un recurso
@Head() // Es lo mismo que el Get pero no devuelve el cuerpo de la respuesta
@All() // Se usa para indicar que acepta todos los métodos
```
Por defecto cada decorador devuelve el http code por defecto pero si lo quieres modificar simplemente usaríamos el decorador
```typescript
@HttpCode(204)
```
Para añadir headers a los controladores usamos el decorador
```typescript
@Post()
@Header('Cache-Control', 'no-store') // Útil para que la caché no guarde infrormación sensible como el perfil de un usuario o datos bancarios
create() {
  return 'This action adds a new cat';
}
```
También podemos redireccionar las peticiones a una URL custom
```typescript
@Get()
@Redirect('https://nestjs.com', 301)
```
Para hacer un endpoint para crear un objeto en este caso gatos primero tenemos que crear una clase de JavaScript, las interfaces no nos sirven ya que en el JavaScript compilado las elimina. 
```typescript
// Clase
export class CreateCatDTO {
	name: string;
	age: number;
	breed: string;
}
// Controlador
@Post()
async create(@Body() createCatDTO: CreateCatDTO) {
	return 'Add new cat'
}
```
Siempre tienen que ser parte de un módulo
# Decoradores
Te ofrecen varios tipos de decoradores para que puedas usar en vez de los tags de express

| `@Request(), @Req()`      | `req`                               |
| ------------------------- | ----------------------------------- |
| `@Response(), @Res()`*    | `res`                               |
| `@Next()`                 | `next`                              |
| `@Session()`              | `req.session`                       |
| `@Param(key?: string)`    | `req.params` / `req.params[key]`    |
| `@Body(key?: string)`     | `req.body` / `req.body[key]`        |
| `@Query(key?: string)`    | `req.query` / `req.query[key]`      |
| `@Headers(name?: string)` | `req.headers` / `req.headers[name]` |
| `@Ip()`                   | `req.ip`                            |
| `@HostParam()`            | `req.hosts`                         |
También puedes crear tus propios decoradores para poder obtener un usuario por ejemplo en express haríamos
```typescript
const user = req.user;
```
Ahora tendríamos que crear un decorador de la siguiente manera
```typescript
import { createParamDecorator, ExecutionContext } from '@nestjs/common';
export const User = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.user;
  },
);
```
Y así poder usar
```typescript
@Get()
async findOne(@User() user: UserEntity) {
  console.log(user);
}
```
También podemos usar los decoradores para pasarle un dato como el token de autentificación de la siguiente manera
```typescript
import { createParamDecorator, ExecutionContext } from '@nestjs/common';
export const User = createParamDecorator(
  (data: string, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    const user = request.user;

    return data ? user?.[data] : user;
  },
);
// Y ahora le pasamos el dato de la siguiente manera
@Get()
async findOne(@User('firstName') firstName: string) {
  console.log(`Hello ${firstName}`);
}
```
# Routes
En NestJs la ruta se define con un tag que engloba a todo el controlador o uno especifico con sus decoradores correspondientes
```typescript
@Controller('cats') // Global
export class CatsController {
  @Get() // Individual
  findAll(): string {
    return 'This action returns all cats';
  }
}
```
También puedes usar algunas características como la de * en las rutas indicando que después puede ir cualquier combinación de caracteres
```typescript
@Get('abcd/*')
findAll() {
  return 'This route uses a wildcard';
}
```
También podemos buscar por Id de la siguiente manera
```typescript
@Get(':id')
findOne(@Param('id') id: string): string {
  return `This action returns a #${id} cat`;
}
```
Query parameters, nos permite incorporar filtros a la ruta de la siguiente manera
```typescript
// GET /cats?age=2&breed=Persian
@Get()
async findAll(@Query('age') age: number, @Query('breed') breed: string) {
  return `This action returns all cats filtered by age: ${age} and breed: ${breed}`;
}
```
# Services


