# Blog meaculpa.dev

My personal blog, hosted on [Deno Deploy](https://deno.com).

## Init project

Initialize git submodule, which contains the used theme "PaperMod":

```
git submodule update --init --recursive
```

### Update theme submodule

Update remote repo for theme `PaperMod` to recent release:

```
git submodule update --remote --merge
```

## Hugo specifics

### Develop

Run development server with HMR:

```
hugo server -D
```

### Add post
```
hugo new --kind post <name>
```
