# 选择健康卡流程

## 举例：图文资讯选择健康卡

1. 分析页面
```javascript
// 1.渲染健康卡
<div className="coupon" onClick={this.selectHealthCard.bind(this)}>
    <div className="coupon-title">
        <span className="coupon-title-span1">健康卡</span>
    </div>
    <div className="coupon-info">
        <span>{this.state.healthCardName}</span>
            <img src="http://pddoc4comm.oss-cn-beijing.aliyuncs.com/ask/more.png" />
    </div>
</div>

// 2.点击事件
selectHealthCard() {
    let that = this;
    if (that.state.healthCardList.length == 0) { // getHealthCards接口拿到健康卡列表 请找 getHealthCards方法
        return;
    }
    if (that.state.quickInquiry == 1) { //快速问诊进入健康卡
        location.href = Utils.getLocationUrl() + 'healthCard/selectHealthCard.html?counselId=' + that.state.counselId + '&quickInquiry=' + that.state.quickInquiry + '&bizType=' + that.state.bizType + '&price=' + that.state.data.price;
    } else {
        location.href = Utils.getLocationUrl() + 'healthCard/selectHealthCard.html?counselId=' + that.state.counselId + '&bizType=' + that.state.bizType + '&price=' + that.state.data.price;
    }
}

```

2. healthCard/selectHealthCard.html 健康卡页面
- healthCard/selectHealthCard/components/HealthCardList.js里面的选择健康卡方法``selectHeathCard``

```javascript
// 这次加入次卡判断逻辑需要加一个次卡的标识 
// 注意：需要和翟智铮统一一下标识
selectHeathCard() {
        let that = this;
        sessionStorage.setItem('healthCardId', that.state.data.id); // 选择健康卡标记
        sessionStorage.setItem('healthCardName', that.state.data.name); // 选择健康卡名字
        //健康卡优先，使用健康卡时默认不使用优惠券
        sessionStorage.setItem('couponId', '');
        sessionStorage.setItem('couponName', '不使用优惠券');
        sessionStorage.setItem('selected', 1);
        if(sessionStorage.getItem('QusDetailIM')){
            let price = JSON.parse(sessionStorage.getItem('QusDetailIM')).price;
            sessionStorage.setItem('couponVa', price);
        }

        // 选择完健康卡返回不同页面的逻辑
        if (navigator.userAgent.indexOf('Android') != -1) {
            history.back();
        } else if (that.state.bizType == 610101 && that.state.quickInquiry != 1) {
            location.href = Utils.getLocationUrl() + 'Pay/payQuestion.html';
        } else if (that.state.bizType != 610101 && that.state.quickInquiry == 1) {
            location.href = Utils.getLocationUrl() + 'Pay/payQuestionIM.html?counselId=' + that.state.counselId + '&quickInquiry=' + that.state.quickInquiry;
        } else {
            location.href = Utils.getLocationUrl() + 'Pay/payQuestionIM.html?counselId=' + that.state.counselId;
        }
    }
```

3. 点击支付按钮

```javascript
submit() {
        let that = this;
        if (!that.state.payBtnEnable) {
            return;
        }
        that.setState({
            payBtnEnable: false
        });

        Toast.loading('', 10000);

        if (that.state.quickInquiry == 1) {
            let params = {};
            if (that.state.healthCardId != '') {
                params = {
                    quickCounselId: that.state.counselId,
                    couponInstanceId: that.state.couponId,
                    needPay: 0,
                    cardId: that.state.healthCardId,
                    sign: ''
                };
            } else {
                params = {
                    quickCounselId: that.state.counselId, // 临时订单ID
                    couponInstanceId: that.state.couponId, // 优惠券标识
                    needPay: that.state.data.price, // 商品价格
                    cardId: that.state.healthCardId, // 健康卡ID
                    sign: ''
                };
            }           
```
- 默认选中的是健康卡列表里面的第一个健康卡
- 获取健康卡ID ``healthCardId`` 提交字段为 ``cardId``
