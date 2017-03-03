---
title: java统计多线程运行时间-countdownlatch的使用
layout: post
tags: '[JAVA,多线程]'
categories: blog
---
我们项目当中使用了spring quartz定时任务，其中一个用了多线程执行，为了统计执行时间，方便性能比较，查阅资料后，发现countdownlatch：

1.以下为使用countdownlatch的部分代码：  
 
    public void run() {
    private static CountDownLatch countDownLatch;
		long startTime=System.currentTimeMillis();
		String stime=String.valueOf(startTime);
		countDownLatch=new CountDownLatch(2);
		if (flag.equals("0")) {
			try {
				Thread t1=new Thread(new Runnable() {
					@Override
					public void run() {
						getTotalMapAndUpdateTables(GlobalConstants.LTESOURCE1);//读取minId,查询S1_ Exception_Data表 minId到maxId之间的并保存到map中
						countDownLatch.countDown();
					}
				});
				Thread t2=new Thread(new Runnable() {
					@Override
					public void run() {
						getTotalMapAndUpdateTables(GlobalConstants.LTESOURCE2);//读取minId,查询S1_ Exception_Data表 minId到maxId之间的并保存到map中
						countDownLatch.countDown();
					}
				});
				t1.start();
				t2.start();
				countDownLatch.await();
				long endTime=System.currentTimeMillis();
				String etime=String.valueOf(endTime);
				logger.info("开始时间为："+stime+" ********");
				logger.info("结束时间为："+etime+" ********");
				logger.info("从多库中的异常分钟表表直接统计到展示库的总分钟表所用时间："+(Long.valueOf(etime)-Long.valueOf(stime))+"    **********");
				} catch (Exception e) {
					logger.error(e);
					e.printStackTrace();
				}
			}
		}
