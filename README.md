# Market-Breadth

计算美股、A股市场宽度

### 数据格式
使用 MySQL 存储数据，目前美股和A股各有两张表，具体表接口参加 config/sql/xxx.sql 文件，以A股为例：
```
zh_stocks_info.sql 生成 zh_stocks_info 表: 股票基础数据
zh_stocks_d.sql 生成 zh_stocks_d 表: 日线周期数据
zh_stocks_sector_sw_d.sql 生成 计算市场宽度依赖的视图
tmp_zh_stocks_sw_sector_d 
zh_stocks_sector_sw_d
```

安装好 mysql 并在 mysql 中运行 config/sql 目录下的文件可以创建好自己的底层表。

另外需要让创建用户并授权，这里密码采用 `123456` ， 自行修改并修改配置文件 config/config.conf 中的配置项：
```SQL
ALTER USER 'stocks'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
grant all privileges on  *.* to 'stocks'@'%';
```

### 数据获取
A 股使用 Tushare 获取，欢迎使用 https://tushare.pro/register?reg=275414 注册邀请链接帮我加积分。
```python 
    pip install tushare
```
申请之后记得替换 config.conf 中的 token 配置项。

美股使用 yfinance 获取：
```python 
    pip install yfinance
```
其他库依赖：
+ opendatatools
+ requests
+ bs4
+ pandas
+ TA-Lib

遇到什么安装什么即可。。。

### 运行分析
#### 1. 获取 A 股市场宽度
+ 1. 获取 A 股全市场股票基本信息：
```python   
    python task/zh_get_info.py
```
+ 2. 计算 A 股市场宽度图表：
```python   
    python task/zh_get_daily.py
```
获取数据时间比较久，请耐心等待，预计 1-2 个小时。
+ 3. 示例：
<div align="center">
	<img src="./data/Market-Breadth-ZH-SW.jpg" width="500">
</div>

#### 1. 获取 美股标普500 市场宽度
注意：美股数据获取最好使用科学上网。
+ 1. 获取美股全市场股票基本信息：
```python   
    python task/us_get_info.py
```
+ 2. 计算 美股标普500 市场宽度图表：
```python   
    python task/us_get_daily.py
```
获取数据时间比较久，请耐心等待，预计 1-2 个小时。
+ 3. 美股示例在 `data/Market-Breadth-US.jpg`

#### 惊喜：
在计算过程中该程序已经计算了全市场股票的 破线、交叉、拐头 等数据，您可以用 sql 在 mysql 中自行分析。

### install on centos6

    mysql -h127.0.0.1 -uroot -p stocks < config/sql/zh_stocks_info.sql
    mysql -h127.0.0.1 -uroot -p stocks < config/sql/zh_stocks_d.sql
    mysql -h127.0.0.1 -uroot -p stocks < config/sql/zh_stocks_sector_sw_d.sql
    mysql -h127.0.0.1 -uroot -p stocks < config/sql/zh_stocks_industries_d.sql

    wget http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
    tar -xzf ta-lib-0.4.0-src.tar.gz
    cd ta-lib/
    sudo ./configure
    sudo make
    sudo make install
    pip3.6 install https://github.com/mrjbq7/ta-lib/archive/TA_Lib-0.4.8.zip

    wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox-0.12.6-1.centos6.x86_64.rpm
    sudo yum install -y fontconfig libX11 libXext libXrender libjpeg libpng xorg-x11-fonts-75dpi xorg-x11-fonts-Type1
    sudo rpm -i wkhtmltox-0.12.6-1.centos6.x86_64.rpm

    pip3.6 install tushare
    pip3.6 install opendatatools requests bs4 pandas TA-Lib sqlalchemy mysql-connector
    pip3.6 install Cython
    pip3.6 install https://github.com/mrjbq7/ta-lib/archive/TA_Lib-0.4.8.zip
    pip3.6 install matplotlib seaborn install imgkit yfinance baostock html5lib pdfkit imgkit

    export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH

