# MPull App Store

MPull 容器应用商店的应用源仓库。MPull 面板从这里读取应用清单、部署配置和图标。

## 目录结构

```
mpull-appstore/
├── apps.json            # 应用索引（列表页数据：名称/版本/分类/图标路径/简介）
├── icons/               # 共享图标目录
└── apps/
    └── <app-id>/
        └── app.json     # 单个应用：完整元数据 + compose + 说明
```

## app.json 字段说明

| 字段 | 必填 | 说明 |
|---|---|---|
| `id` | ✅ | 应用唯一标识（小写字母数字），同时是默认 Stack 名 |
| `name` | ✅ | 显示名称 |
| `version` | ✅ | 应用配置版本 |
| `description` | ✅ | 一句话简介 |
| `categories` | ✅ | 分类数组，如 `["网络工具"]` |
| `icon` | ✅ | 图标路径（相对仓库根目录，如 `icons/lucky.png`） |
| `compose` | ✅ | 完整 compose YAML 字符串 |
| `default_stack_name` | ➖ | 默认 Stack 名，缺省用 `id` |
| `note` | ➖ | 安装注意事项 |
| `author` / `image` | ➖ | 作者 / 主镜像 |

## 安装目录占位符

compose 中宿主机路径可以用 `{{docker_root}}` 占位符，部署时会替换为用户在 MPull 商店设置中配置的「容器安装目录」，例如用户配置 `/vol4/1000/docker`，则：

```yaml
volumes:
  - "{{docker_root}}/lucky/luckyconf:/app/conf"
```

部署后实际为：

```yaml
volumes:
  - "/vol4/1000/docker/lucky/luckyconf:/app/conf"
```

## 如何贡献应用

Fork 本仓库 → 在 `apps/<app-id>/app.json` 添加应用配置、`icons/` 放图标、`apps.json` 加索引条目 → 提交 PR。第三方维护者也可以直接维护自己的 fork，并在 MPull 商店设置中添加 fork 仓库地址（格式 `用户名/仓库名`）。
