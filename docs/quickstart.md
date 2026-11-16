# 快速开始：先得到一个可用结果

本指南不执行安装，也不授予操作权限。使用本库的最小条件是：用户目标与范围明确、所需原文可读、至少一种获准工作方式可用。Dot、多代理、Git 写权限和后台通知都不是阅读方法的前提。

## 1. 先确定采用模式

**只有普通聊天：**用网页版分析和编写任务包；需要其他环境执行时，把确切文本或授权文件交用户/协调者转交。没有工具就不声称已改代码或已发消息。

**一个可执行 AI：**边界清楚的任务可在同一会话规划、实现、自检、修复和交付；只在触发高风险审查时请求独立核对，不先搭建多代理体系。

**已有网页与本地协作：**保留实际网页作者、主控和实现者。Dot 只填补真实交接/核查缺口；已有直接交接就沿用，不制造强制中转。

## 2. 核对候选与能力

阅读 [STATE](../STATE.md)、[公共入口](../AGENTS.md) 与 [部署前清单](preflight.md)。核交付文件版本、外部清单及授权范围，不能只看仓库名称。当前候选仍需独审与相应采用，不是安装即生效。

对本轮实际需要的能力做最小核对：固定版本读取、目标文件可写范围、终端/浏览器操作、模型路由、隔离会话、任务回执、完成通知和计费。无关能力不必探查；支持未知时保留未知，不扫描全部账号或连接器。

## 3. 新部署与旧项目分别处理

新部署：由获授权维护者在独立非同步目录核对文件，在独审与建仓授权满足后建立空私有仓库，仅提交确认过的通用文件。实际仓库地址、提交身份及本机路径只放部署记录，不填回公共示例。

旧项目：不要直接复制根 AGENTS 覆盖项目专属规则。按 [迁移指南](migration.md) 先核旧规则、停点与真实版本，准备最小指针变化和回退输入。暂停项目保持暂停；规则通知不能启动它。

## 4. 建立一个薄入口

在目标项目私有位置参考 [项目模板](../templates/project-AGENTS.md)，只填真实身份、已采用规则的固定提交或有界清单、环境、要求来源、模型例外、预算及停点。动态进度留原记录，不复制公共正文。

在网页版项目说明参考 [入口模板](../templates/web-public-project-instructions.txt)。要求实际读取完整公共入口、相应环境及本次阶段。工具是否自动发现 AGENTS 必须实测；不会自动加载时用清楚的读取指令或获准附件，不宣称已永久记住。

## 5. 发出一个完整结果请求

给用户可见目标、非目标、现有入口/版本和授权边界。例如虚构的离线清单任务见 [示例](../examples/offline-list-scenario.md)。网页版输出明确任务包，不把产品含义留给 Dot 猜；用户授权执行后才转交。

本地主控核当前工作区与差异后，选一个主要实现者接续。原成果复用，制作和验收准备同步；内部机制不必各自开任务。仅真正独立、有净收益的工作并行。

## 6. 完成与反馈

主要实现者先完成内部真实操作、保存/恢复及必要独审，再给用户确切入口、可体验范围、进度来源和限制。用户反馈进入原任务：技术缺陷由原作者合批修，真实产品变化返回网页设计。没有新信息则退避，已有暂停等明确恢复。

## 可选：离线核对交付清单

下面只读取已取得的候选和清单，不安装依赖、不联网、不修改文件。需要本机已经有可用 Python 3。将两个参数替换为实际获准的相对路径；清单格式为 `files` 数组，行内含 `path`、`bytes`、`sha256`。

```sh
python3 - "候选仓库目录" "外部清单.json" <<'PY'
import hashlib, json, sys
from pathlib import Path, PurePosixPath
root = Path(sys.argv[1]).resolve()
manifest = json.loads(Path(sys.argv[2]).read_text(encoding='utf-8'))
seen = set()
for row in manifest['files']:
    rel = PurePosixPath(row['path'])
    assert not rel.is_absolute() and '..' not in rel.parts, '不安全路径'
    assert str(rel) not in seen, '重复路径'
    seen.add(str(rel))
    path = root.joinpath(*rel.parts)
    assert not path.is_symlink(), '不接受符号链接'
    assert root in path.resolve().parents and path.is_file(), '文件不可读'
    data = path.read_bytes()
    assert len(data) == row['bytes'], f'长度不符: {rel}'
    assert hashlib.sha256(data).hexdigest() == row['sha256'], f'哈希不符: {rel}'
    assert not data.startswith(b'\xef\xbb\xbf') and b'\r' not in data, '编码/行尾不符'
    data.decode('utf-8')
actual = {p.relative_to(root).as_posix() for p in root.rglob('*') if p.is_file()}
assert actual == seen, '清单外文件或缺失文件'
print('文件字节与清单一致；不代表安全审查、授权或采用通过。')
PY
```

仅在新解压且未加入 `.git` 的候选目录使用全文件集合核对；已有工程不能据此删除额外文件。交付后发生合法编辑，应生成新的清单，不沿用旧身份。
