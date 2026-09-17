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
| `default_stack_name` | ➖ | 默认 Stack 名，缺省用 `id` |
| `note` | ➖ | 安装注意事项 |
| `author` / `image` | ➖ | 作者 / 主镜像 |

compose 文件就是标准 docker compose YAML，在 GitHub 上可直接编辑并语法高亮。

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

## 自定义占位符（安装者填写）

除 `{{docker_root}}` 外，compose 中可以用任意 `{{名称}}` 占位符（如 `{{media_dir}}`）表示需要安装者自行填写的宿主机路径，媒体目录、下载目录这类因人而异的路径都用这种方式：

```yaml
volumes:
  - "{{media_dir}}/:/media"
```

MPull 安装弹窗会自动识别这些占位符：安装者直接修改弹窗中的 compose，把占位符替换为实际路径后部署；存在未填写的占位符时会拒绝部署并提示。占位符的说明可以直接写在 compose 的 `#` 注释里（注释行中的占位符不参与校验）。

## 如何贡献应用

Fork 本仓库 → 在 `apps/<app-id>/` 下添加 `app.json`（元数据）与 `compose.yml`（部署配置）、`icons/` 放图标、`apps.json` 加索引条目 → 提交 PR。第三方维护者也可以直接维护自己的 fork，并在 MPull 模板设置中添加 fork 仓库地址（格式 `用户名/仓库名`）。
