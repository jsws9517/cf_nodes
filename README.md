# Cloudflare 节点测速工具

一个用于测试 Cloudflare CDN 节点性能的工具集，可以扫描 IP 段、测速节点延迟和带宽，并生成详细的测试报告。

## 项目简介

本项目基于 CloudflareST 测速工具，提供了一套完整的 Cloudflare 节点扫描和测速解决方案。主要功能包括：

- **IP 段扫描**：扫描 Cloudflare 的 IP 地址段，发现可用节点
- **性能测速**：测试节点的延迟、丢包率和下载速度
- **地区识别**：自动识别节点所在的地区（如 SEA、HKG 等）
- **结果导出**：将测试结果导出为 CSV 格式，便于分析和筛选

## 文件说明

### 核心工具

- `CloudflareST_proxy_win_amd64.exe`：CloudflareST 测速工具（Windows 64位版本）
- `ip_node_sniffer.exe`：IP 节点嗅探工具，用于扫描 IP 段并生成节点列表

### 配置文件

- `.cloudflare_speedtest_config.json`：测速工具配置文件

**配置示例**：

```json
{
  "github_token": "your_github_token_here",
  "repo_info": "username/repository",
  "file_path": "cloudflare_ips.txt",
  "github_last_used": "2026-01-01 12:00:00"
}
```

**字段说明**：
- `github_token`：GitHub 个人访问令牌（用于自动更新 IP 列表）
- `repo_info`：存储 IP 列表的 GitHub 仓库
- `file_path`：仓库中 IP 列表文件的路径
- `github_last_used`：上次同步时间

### 数据文件

- `Cloudflare.txt`：Cloudflare 官方 IP 地址段列表（CIDR 格式）
- `ips_ports.txt`：待测试的 IP:端口 列表
- `result.csv`：测速结果文件（最终筛选结果）
- `region_scan.csv`：按地区扫描的结果文件

**CSV 字段说明**：

| 字段 | 说明 |
|------|------|
| IP 地址 | 节点的 IP 地址 |
| 已发送 | 发送的数据包数量 |
| 已接收 | 接收的数据包数量 |
| 丢包率 | 丢包率（%） |
| 平均延迟 | 平均延迟（ms） |
| 下载速度 | 下载速度（MB/s） |
| 地区码 | 节点地区代码（如 SEA、HKG、SJC 等） |
| 端口 | 测试的端口号 |

## 使用方法

### 1. 准备 IP 列表

**方式一：使用 Cloudflare 官方 IP 段**

`Cloudflare.txt` 已包含 Cloudflare 官方公布的 IP 地址段：

```
173.245.48.0/20
103.21.244.0/22
103.22.200.0/22
...
```

**方式二：手动添加 IP**

在 `ips_ports.txt` 中添加要测试的 IP 和端口，格式为 `IP:端口`：

```
104.18.1.16:443
172.67.165.27:443
...
```

### 2. 配置 GitHub 同步（可选）

如果需要自动从 GitHub 更新 IP 列表，编辑 `.cloudflare_speedtest_config.json`：

```json
{
  "github_token": "your_github_token_here",
  "repo_info": "your_username/your_repo",
  "file_path": "cloudflare_ips.txt"
}
```

**获取 GitHub Token**：
1. 访问 GitHub Settings > Developer settings > Personal access tokens
2. 生成新的 token（建议仅勾选 `repo` 权限）
3. 将 token 填入配置文件

### 3. 运行测速

**使用图形界面（推荐）**：

```bash
CloudflareST_proxy_win_amd64.exe
```

**使用命令行**：

```bash
# 扫描 IP 段并生成节点列表
ip_node_sniffer.exe

# 测速并导出结果
CloudflareST_proxy_win_amd64.exe -f ips_ports.txt -o result.csv
```

### 4. 查看结果

测速完成后，打开 `result.csv` 查看结果。可以按以下条件筛选优质节点：

- **低延迟**：平均延迟 < 200ms
- **低丢包**：丢包率 = 0%
- **高带宽**：下载速度 > 10 MB/s

**示例结果**：

```csv
IP 地址,已发送,已接收,丢包率,平均延迟,下载速度(MB/s),地区码,端口
104.18.x.x,4,4,0.00,194.23,33.68,SEA,443
172.67.x.x,4,4,0.00,194.90,29.29,SEA,443
```

## 地区代码说明

常见的地区代码：

| 代码 | 地区 |
|------|------|
| SEA | 西雅图 |
| SJC | 圣何塞 |
| LAX | 洛杉矶 |
| HKG | 香港 |
| NRT | 东京 |
| SIN | 新加坡 |
| FRA | 法兰克福 |
| LHR | 伦敦 |

## 注意事项

1. **GitHub Token 安全**：请勿将包含真实 token 的配置文件提交到公共仓库
2. **IP 地址更新**：Cloudflare IP 地址段可能会变化，建议定期更新
3. **测速环境**：测速结果受网络环境影响，建议多次测试取平均值
4. **合规使用**：请遵守当地法律法规和 Cloudflare 服务条款

## 致谢

- [CloudflareST](https://github.com/XIU2/CloudflareSpeedTest) - Cloudflare CDN 延迟测速工具
- [Cloudflare](https://www.cloudflare.com/) - CDN 服务提供商

## 免责声明

本项目仅供学习和研究使用。使用本工具时请遵守相关法律法规，作者不对因使用本工具产生的任何问题负责。
