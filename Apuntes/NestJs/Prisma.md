Primero de todo tener instalado prisma e iniciarlo 

```bash
npm install prisma --save-dev
npx prisma
npx prisma init
```
En el archivo de config hay que añadir ya que 
```
generator client {
  moduleFormat = "cjs"
}
```