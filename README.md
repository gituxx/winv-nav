# WinV 导航

纯静态单页导航，部署于 Cloudflare Pages：https://nav.winv.cc

编辑 `public/index.html` 中的 `D` 数组即可增删项目，然后：

```
npx wrangler pages deploy public --project-name winv-nav --branch main
```
