# 联调 RPC 的期限继承与分支取消协调

仿真时钟以到达秒点表现。父请求和子调用使用明确身份，所有材料均为本地合成标识。

## 输入消息

每个 JSONL 文件是一段独立输入记录。每行包括整数秒点 at、消息名称 kind 与对象 record。at 代表中间件收到消息的时刻，同一秒内按行序提交。业务时间和对应回执引用写在 record 中。

- **root**　提交 root、reply_by 和 reserve，reserve 为末端核对保留秒数。
- **call**　提交 root 的 child，parent 可引用既有 child，kind 为 pure、prepare 或 cleanup，cleanup 自带 cleanup_until。材料占用使用 asset。 now 达到或超过普通子调用的预算末点时不再受理新普通工作。
- **accepted**　执行端实际接受此 child，若 prepare 则确认占用。
- **result**　回报 child 的 done、not_started、released 或 unknown。
- **cancel**　终端取消明确 root。
- **restart**　协调器重启。
- **inspect**　查看 root 或资产。

## 示例

- cancel-leaves-cleanup.jsonl
- new-descendant-near-cutoff.jsonl

## 读取演练资料

运行环境为 Python 3.11，只使用标准库。在根目录运行以下命令可核对文件格式及读取接口。

    python3 scenario_reader.py examples
    python3 -m unittest discover -v

资料读取器会按文件统计各类参与方消息的数量。演练记录只描述各参与方交给系统的输入。
