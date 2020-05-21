---
layout: article
title: 用BP神经网络模型预测航空公司客户流失程度 
author: 沈卓洋
date: 2020-5-21 21:35:00 +0800
fig-caption: # Add fig caption (optional)
tags: [MATLAB]
---
前几天看到一个用BP神经网络预测航空公司客户流失的数学模型，如下图
![img](/assets/images/2020-5-21/customer.png)
给了六万多条乘客的各类信息，包括会员卡号、级别、年龄、性别、工作地这些基本信息，以及与积分和乘机次数相关的乘机信息。

### 数据探索和清洗
首先进行数据探索和清洗，用来清楚那些出错的、无意义的乘客信息（例如乘机次数是0但里程大于0），这部分程序就不放上来了；
### 老客户筛选
本题的客户流失分析是针对老客户进行的，也就是说我们先把乘机数量大于6的筛选出来，然后把筛选剩下的作为新客户，用来老客户训练出来的模型预测未来的的客户类型（流不流失）。
```MATLAB
%% 筛选老客户和新客户
clc;
clear;

% 参数初始化
datafile = '../tmp/data_cleaned.csv';		% 从清洗过的客户里选
savefile_old = '../tmp/customer_old.csv' ;	% 筛选用于训练的老客户
savefile_new = '../tmp/customer_new.csv';	% 筛选用于预测的新客户

% 读取文件
data=xlsread(datafile);
[row,col]=size(data);

% 筛选型老客户
num_old = 1;	%统计共有多少老客户
num_new = 1;	%统计共有多少新客户
for i=1:row
    if data(i,11) > 6 
        old_custom(num_old,:) = data(i,:);
        num_old = num_old + 1;
    else
        new_custom(num_new,:) = data(i,:);
        num_new = num_new + 1;
    end
end
disp(['共筛选出老客户' num2str(num_old - 1) '位']);
disp(['共筛选出新客户' num2str(num_new - 1) '位']);

% 数据写入
csvwrite(savefile_old,old_custom);
csvwrite(savefile_new,new_custom);
disp('数据写入完成！');

```
### 参数筛选
我们筛选了筛选会员卡级别、观察窗口的总加权飞行公里数、平均乘机时间间隔、积分兑换次数、平均折扣率、非乘机积分总和、单位里程票价、单位里程积分和第一年乘机次数、第二年乘机次数这10个参数，其中前8个是输入层的参数，后2个是目标层的参数。

老客户直接写入了这10个参数，用来预测的新客户写入了前8个参数（之后用来预测），以及会员卡号（标识最后的预测结果）;

这里为什么没直接将客户类型（即第二年乘机次数与第一年乘机次数之比）作为目标层，是因为比值很难变成线性的，并且直接取比值存在奇异样本数据（即有的客户第一年乘机次数为0，有的为1），我一开始是这么做的，但是发现这样做即使标准化、归一化之后训练效果都很差。

```MATLAB
%% 筛选参数
clc;
clear;
% 参数初始化
datafile_old = '../tmp/customer_old.csv';
datafile_new = '../tmp/customer_new.csv';
savefile_old = '../tmp/ParameterSelected_old.csv';      % 参数选完之后的文件
savefile_new = '../tmp/ParameterSelected_new.csv';

% 读取数据
data_old = xlsread(datafile_old);
data_new = xlsread(datafile_new);

% 筛选参数
[row_old,col_old] = size(data_old);  
[row_new,col_new] = size(data_new);  
para_old_1 = data_old(:,5); 
para_new_1 = data_new(:,5); 	% 会员卡级别
para_old_2 = data_old(:,18);
para_new_2 = data_new(:,18);	% 观察窗口的总加权飞行公里数
para_old_3 = data_old(:,24);  
para_new_3 = data_new(:,24);	% 平均平均乘机时间间隔
para_old_4 = data_old(:,28);  
para_new_4 = data_new(:,28);	% 积分兑换次数
para_old_5 = data_old(:,29);  
para_new_5 = data_new(:,29);	% 平均折扣率
para_old_6 = data_old(:,36);  
para_new_6 = data_new(:,36);	% 非乘机积分总和
count_test = 1;
for i=1:row_old
    para_old_7(i,1) = (data_old(i,15)+data_old(i,16))/data_old(i,17);	% 单位里程票价
    para_old_8(i,1) = data_old(i,38)/data_old(i,17);	% 单位里程积分
    para_old_9(i,1) = data_old(i,30);	% 第一年乘机次数
    para_old_10(i,1) = data_old(i,31);	% 第二年乘机次数
end
for i=1:row_new
    para_new_7(i,1) = (data_new(i,15)+data_new(i,16))/data_new(i,17);	% 单位里程票价
    para_new_8(i,1) = data_new(i,38)/data_new(i,17);	% 单位里程积分
    para_new_9(i,1) = data_new(i,1);	% 会员卡号
end

% 写入筛选后的数据
xlswrite(savefile_old, [ para_old_1 , para_old_2 , para_old_3 , para_old_4 , para_old_5 ,  para_old_6 , para_old_7 , para_old_8 ,  para_old_9, para_old_10 ]);
xlswrite(savefile_new, [ para_new_1 , para_new_2 , para_new_3 , para_new_4 , para_new_5 ,  para_new_6 , para_new_7 , para_new_8 , para_new_9 ]);
disp('数据写入完成！');
```

### 数据随机分类

按要求我们将数据的80%作为训练集，20%作为测试集

```MATLAB
%% 老客户随机分成训练集和测试集
clc;
clear;

% 参数初始化
datafile =  '../tmp/ParameterSelected_old.csv';	% 完整的输入层数据
savefile_train_input = '../train/input.csv';    % 用来训练的输入层数据
savefile_train_target = '../train/target.csv';  % 用来训练的目标层数据
savefile_test_input = '../test/input.csv';      % 用来测试的输入层数据
savefile_test_target = '../test/target.csv';    % 用来测试的目标层数据 

% 数据读取
data = xlsread(datafile);
[row,col] = size(data);

% 数据随机分类
random = rand(1,row); % 产生均匀分布的伪随机数列
num_train = 1;        
num_test = 1;
for i=1:row
    if random(1,i) < 0.8
        train_input(num_train,1:8) = data(i,1:8);
        train_target(num_train,1) = data(i,9);
        train_target(num_train,2) = data(i,10);
        num_train=num_train + 1;
    else 
        test_input(num_test,1:8) = data(i,1:8);
        test_target(num_test,1) = data(i,9);
        test_target(num_test,2) = data(i,10);
        num_test=num_test + 1;
    end
end
disp(['训练集共有数据：' num2str(num_train - 1) ]);
disp(['预测集共有数据：' num2str(num_test - 1) ]);

% 数据写入
csvwrite(savefile_train_input,train_input);
csvwrite(savefile_train_target,train_target);
csvwrite(savefile_test_input,test_input);
csvwrite(savefile_test_target,test_target);
disp('数据写入完成！');
```

### BP神经网络训练和预测
首先我们要进行数据的标准化，顺便将矩阵转置了一下（标准化函数和神经网络训练的时候都要求列为样本，而老师给的恰好反了），这里我们用的是mapminmax函数，标准化之后用神经网络跑吧，经过反复训练和调参，发现使用神经元数量是10、最大迭代次数是2000、训练的误差进度是0.001、学习速度是0.05、变学习率梯度下降算法效果相对比较好；接着就用它预测吧

```MATLAB
%% 利用BP神经网络训练和测试，并且预测新客户的客户类型
clear;
clc;

% 参数初始化
trainfile_input = '../train/input.csv';          % 训练集的输入层文件；
trainfile_target = '../train/target.csv';        % 训练集目标层文件；
testfile_input = '../test/input.csv';            % 测试集的输入层文件；
testfile_target = '../test/target.csv';          % 测试集的目标层文件；
predictfile = '../tmp/ParameterSelected_new.csv';% 预测集
savefile = '../result/predict.csv';              % 预测结果

% 读取数据
P_train = csvread(trainfile_input)';
T_train = csvread(trainfile_target)';
P_test = csvread(testfile_input)';
T_test = csvread(testfile_target)';
predict = xlsread(predictfile);
P_predict = predict(:,1:8)';

% 数据归一化
[P_train, ps_input] = mapminmax(P_train , -1 , 1);
P_test = mapminmax('apply' , P_test , ps_input);
P_predict = mapminmax('apply' , P_predict , ps_input);
[T_train, ps_output] = mapminmax(T_train , 0 , 1);

%创建一个BP神经网络，每一个输入向量的取值范围为[-1 ,1]，隐含层有10个神经元，输出层有2个神经元，隐含层的激活函数为tansig，输出层的激活函数为logsig，训练函数为梯度下降函数，即标准学习算法
net=newff([-1 1;-1 1;-1 1;-1 1;-1 1;-1 1;-1 1;-1 1],[10,2],{'tansig','logsig'},'traingda');
net.trainParam.epochs = 2000;	%　最大迭代次数
net.trainParam.goal = 0.001;	%　训练的误差精度
LP.lr = 0.05;					% 设置学习速率为0.05
net = train(net,P_train,T_train);
T_sim = sim(net,P_test);
T_sim = mapminmax('reverse',T_sim,ps_output);	% 反归一化

%% 测试数据
N = size(T_test,2);
[r,m,b] = regression(T_test,T_sim);
plotregression(T_test,T_sim)
count=1;    %用来计数验证正确的数量
for i=1:N
    if T_test(2,i)/T_test(1,i)<0.5 && T_sim(2,i)/T_sim(1,i)<0.5
        count=count+1;
    elseif T_test(2,i)/T_test(1,i)>0.9 && T_sim(2,i)/T_sim(1,i)>0.9
        count=count+1;
    elseif T_test(2,i)/T_test(1,i)>0.5 && T_sim(2,i)/T_sim(1,i)>0.5 && T_test(2,i)/T_test(1,i)<0.9 && T_sim(2,i)/T_sim(1,i)<0.9
        count=count+1;
    end
end
disp(['预测正确率:' num2str(count/N)]);

%% 预测数据
if count/N > 0.5 
    T_predict = sim(net,P_predict);
    M = size(T_predict,2);
    T_predict = mapminmax('reverse',T_predict,ps_output);
    for i = 1:M
        if T_predict(2,i)/T_predict(1,i) < 0.5
            results(i,1) = -1;  % -1表示已经流失
        elseif T_predict(2,i)/T_predict(1,i) > 0.9
            results(i,1) = 1;   % 1表示未流失
        else
            results(i,1) = 0;   % 0表示准流失
        end
    end
    predict = [predict(:,9) , results];
    xlswrite(savefile,predict);
else disp('测试结果不理想，宁还是重新训练或者继续调参吧233333')
end
```
###　模型的缺点

* 仅仅对老客户进行了粗略的筛选，认为乘机次数>6就是老客户，我感觉这个界定标准不严格和准确，事实上筛选出来的32565位老客户中有相当一部分客户根本不在乎选择的什么航空公司，因为有一部分老客户两年积分都没兑换过，非乘机积分也没领取过，人根本不在乎坐的是谁家的航班，这部分老客户的数据参考价值不大；
* 我们训练出来的模型是老客户的客户流失模型，那它来预测新客户的客户流失，肯定不准确
* 我感觉题目本身就有问题，预测的输入层和目标层怎么说都不应该是一个时间尺度上的东西，而这里的目标层用两年乘机次数之比来定义，它们与预测他们的输入层参数几乎同时发生，因此叫预测还不如叫联系更贴切；
* 训练出来的效果还不如直接用图形界面的神经网络工具点点点来的效果好，属实拉跨，可能参数还是什么还需要调调。