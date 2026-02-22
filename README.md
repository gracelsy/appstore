# 自建1Panel应用商店

这是一个由 小小粉刷匠 自建维护的 1Panel 第三方应用商店仓库，用于收纳个人常用的容器化应用与预设配置，基于 1Panel 的 App Store 架构。

---

## 🛠 使用说明

你可以将本仓库作为第三方 App Store 添加至 1Panel，即可在 Web 面板中浏览、安装、管理其中的应用。

---

### 添加第三方应用仓库

参考官方文档：[📚 如何添加第三方应用仓库](https://github.com/1Panel-dev/appstore/wiki/%E5%A6%82%E4%BD%95%E6%8F%90%E4%BA%A4%E8%87%AA%E5%B7%B1%E6%83%B3%E8%A6%81%E7%9A%84%E5%BA%94%E7%94%A8)

---

## 🔄 同步更新脚本

以下是自动同步 App 应用至 1Panel 的脚本，适用于开发或部署用户。

### 📥 国内同步脚本：

镜像仓库地址：https://cnb.cool/gracelsy/appstore

使用github action保持同步更新。

```bash
#!/bin/bash

set -euo pipefail
IFS=$'\n\t'

MATCH_PATTERN="gracelsy-*"
GIT_REPO="https://cnb.cool/gracelsy/appstore"
TMP_DIR="/opt/1panel/resource/apps/local/appstore-main"
LOCAL_APPS_DIR="/opt/1panel/resource/apps/local"

# 退出时自动清理临时目录
trap 'rm -rf "$TMP_DIR"' EXIT

# 清理旧文件（通配符不加引号）
if [ -d "$LOCAL_APPS_DIR" ]; then
    cd "$LOCAL_APPS_DIR" || exit 1
    rm -rf $MATCH_PATTERN
    echo "🗑️ 删除 ${LOCAL_APPS_DIR} 下所有 ${MATCH_PATTERN} 目录成功"
else
    echo "⚠️  目标目录 ${LOCAL_APPS_DIR} 不存在，无需删除"
fi

# 克隆仓库
echo "📥 Cloning appstore repo..."
[ -d "$TMP_DIR" ] && rm -rf "$TMP_DIR"
git clone "$GIT_REPO" "$TMP_DIR"

# 同步应用
mkdir -p "$LOCAL_APPS_DIR"
for app_path in "$TMP_DIR/apps/"*; do
    [ -d "$app_path" ] || continue
    app_name=$(basename "$app_path")
    local_app_path="$LOCAL_APPS_DIR/$app_name"

    echo "🔁 Updating app: $app_name"
    [ -d "$local_app_path" ] && rm -rf "$local_app_path"
    cp -r "$app_path" "$local_app_path"
done

echo "✅ Sync completed."
```

🌍 国外环境请替换为 GitHub 仓库：

```bash
GIT_REPO="https://github.com/gracelsy/appstore"
```

------