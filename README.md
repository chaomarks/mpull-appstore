# MPull App Store

MPull 容器模板中心的应用源仓库。MPull 面板从这里读取模板清单、部署配置和图标。

## 目录结构

```
mpull-appstore/
├── apps.json            # 应用索引（列表页数据：名称/分类/图标路径/简介）
├── icons/               # 共享图标目录
└── apps/
    └── <app-id>/
        ├── app.json     # 单个应用：完整元数据 + 说明
        └── compose.yml  # 部署用 compose 文件（标准 YAML，可直接编辑）
```

## app.json 字段说明

| 字段 | 必填 | 说明 |
|---|---|---|
| `id` | ✅ | 应用唯一标识（小写字母数字），同时是默认 Stack 名 |
| `name` | ✅ | 显示名称 |
| `description` | ✅ | 一句话简介 |
| `categories` | ✅ | 分类数组，如 `["网络工具"]` |
| `icon` | ✅ | 图标路径（相对仓库根目录，如 `icons/lucky.png`） |
| `compose_file` | ✅ | compose 文件路径（相对仓库根目录，如 `apps/lucky/compose.yml`），推荐写法 |
| `compose` | ➖ | 内联 compose YAML 字符串（旧写法，与 `compose_file` 二选一） |
| `compose_variants` | ➖ | 多版本 compose：`[{"name": "精简版", "file": "apps/x/compose.yml"}, {"name": "完整版", "file": "apps/x/compose.full.yml"}]`，**第一个为默认版本**；部署弹窗顶部会出现版本切换按钮，点一下替换下方 compose（仍可手改） |
| `default_stack_name` | ➖ | 默认 Stack 名，缺省用 `id` |
| `note` | ➖ | 备注或安装注意事项，支持换行和长文本；GitHub 仓库地址会在 MPull 详情中显示为可点击图标 |
| `author` / `image` | ➖ | 作者 / 主镜像 |

compose 文件就是标准 docker compose YAML，在 GitHub 上可直接编辑并语法高亮。

新模板的索引条目请**追加到 `apps.json` 的 `apps` 数组末尾**：数组顺序即上架顺序，模板中心默认倒序展示（最新上架的排最前）。

## 安装目录占位符

compose 中宿主机路径可以用 `{{docker_root}}` 占位符，部署时会替换为用户在 MPull 设置中配置的「容器安装目录」，例如用户配置 `/vol4/1000/docker`，则：

```yaml
volumes:
  - "{{docker_root}}/lucky/luckyconf:/app/conf"
```

部署后实际为：

```yaml
volumes:
  - "/vol4/1000/docker/lucky/luckyconf:/app/conf"
```

`{{docker_root}}` 由 MPull 自动替换，compose 里不必再写注释说明。

## 自定义占位符（安装者填写）

除 `{{docker_root}}` 外，compose 中可以用任意 `{{名称}}` 占位符（如 `{{media_dir}}`）表示需要安装者自行填写的宿主机路径，媒体目录、下载目录这类因人而异的路径都用这种方式：

```yaml
volumes:
  - "{{media_dir}}/:/media"
```

MPull 安装弹窗会自动识别这些占位符：安装者直接修改弹窗中的 compose，把占位符替换为实际路径后部署；存在未填写的占位符时会拒绝部署并提示。占位符的说明可以直接写在 compose 的 `#` 注释里（注释行中的占位符不参与校验）。

## 登录背景（login-bg/）

本仓库同时提供 MPull 的远程登录背景：把图片（png / jpg / jpeg / webp / gif）或视频（mp4 / webm / ogv）放到 `login-bg/` 目录，再把文件名登记到根目录的 `login-bg.json`：

```json
{
  "updated_at": "2026-11-05",
  "files": ["whale.mp4", { "file": "wallpaper.webp", "name": "壁纸" }]
}
```

MPull 的「设置 → 其他设置 → 登录背景 → 选择背景」会从**模板源仓库**（默认就是本仓库）读取这些文件并显示缩略图（10 分钟缓存，弹窗里点「刷新」立即生效），不需要重新打包镜像。Fork 本仓库并在 MPull 模板设置里换成自己的 fork 后，模板和登录背景会一起用你的仓库。

## 如何贡献应用

Fork 本仓库 → 在 `apps/<app-id>/` 下添加 `app.json`（元数据）与 `compose.yml`（部署配置）、`icons/` 放图标、`apps.json` 加索引条目 → 提交 PR。第三方维护者也可以直接维护自己的 fork，并在 MPull 模板设置中添加 fork 仓库地址（格式 `用户名/仓库名`）。
