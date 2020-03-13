---
title: "swoole作为定时任务服务器的用法"
date: 2018-03-29T15:57:06+08:00
tags: ["php"]
author: "七哥"
categories: ["php"]
---

![swoole数据走向示意图](/img/swoole.png)   

## 初始化服务
- 启动server服务，并指出worker_num、task_worker_num等参数，以便于启动worker、task进程

## on绑定处理流程
>上图中的数据A流向，是要经过on的绑定才会正确执行。

1. onReceive，绑定此回调函数worker才会正确接收从reactor中传递过来的数据；（此回调函数在worker进程中执行）
2. onReceive中，调用server->task()，把任务发往task进程中异步执行，并立即空出worker进程，时刻保持有空闲worker可用
3. onTask，绑定此回调函数，worker中的server->task()才会找到可用的task进程，把数据传递给task去具体执行业务流程（此回调函数在task_worker中运行）   
4. onFinish，task函数体以return语法结尾，此回调函数才会接收到来自task的执行结果（此回调函数在worker进程中执行）

## 使用addProcess开启自定义进程服务
- addProcess可以开起自定义的进程，进程中加入swoole内置的心跳方法：swoole_timer_tick，每m毫秒执行一次callback方法

## 后记
```
上述方式建立的swoole服务，可用于替代liunx的cron定时任务，减少了频繁修改定时任务对运维产生的依赖。
上述代码是每M秒运行一次任务，可以做成每M秒读取一次cron_mission_table（表中存储有大量的定时任务以及其重复之心的时间等数据），这样就可以做成任务时间多样化的场景
```

## 实例
```
namespace console\controllers;

use yii\console\Controller;

class SwooleTimerController extends Controller
{
    public function actionStartTimer()
    {
        $server = new \swoole_server(0.0.0.0, 9001);

        $server-set([
            reactor_num = 1, //reactor thread num
            worker_num = 1,    //worker process num
            task_worker_num = 1,    //worker process num
            max_request = 1000,
            dispatch_mode = 1,
        ]);

        $server-on(task, function (\swoole_server $server, $taskId, $fromWorkerID, $data) {
            echo {$taskId}已经接收到来自worker： {$fromWorkerID}的数据{$data} \n;

            return 结果已经产生：来自{$fromWorkerID}的数据{$data}已经处理完成;
        });
        $server-on(finish, function (\swoole_server $server, $taskId, $data) {
            echo {$taskId}已经完成处理，处理结果为：{$data} \n;
        });

        $server-on(receive, function (\swoole_server $server, $fd, $fromID, $data) {
            echo 接收到来自reactor:{$fromID}的数据{$data} \n;
            $result = $server-task($data);

            echo 来自{$fromID}的数据{$data}已投递,处理结果wei :{$result};
        });

        $server-on(shutDown, function (\swoole_server $server) {
            echo swoole 定时任务服务器正在关闭 \n;
        });

        //新增一个process，作为定时任务
        $process = new \swoole_process(function () {
            swoole_timer_tick(1000, function () {
                $client = new \swoole_client(SWOOLE_SOCK_TCP);
                if(!$client-connect(0.0.0.0,9001,10)){
                    exit(connect error: . $client-errCode);
                }
                $client-send(time());
            });
        });
        $server-addProcess($process);
        
        $server-start();
    }
}
```
