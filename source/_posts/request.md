---
title: request
date: 2020-04-30 10:50:27
tags:
---
https原生
        // let options = {
        //     'method': 'post',
        //     'pfx': pfx,
        //     'passphrase': pay_conf.mchid,
        // };
        // // options.agent = new https.Agent(options);
        // const req = https.request(url, options, res => {
        //     console.log('状态码:', res.statusCode);
        //     console.log('请求头:', res.headers);
        //     let data = '';
        //     res.on('data', d => {
        //         data += d;
        //     });
        //     res.on('end', () => {
        //         console.log(data);
        //     });
        // });
        // req.on('error', e => {
        //     console.error(e);
        // });
        // req.write(xmlOption);
        // req.end();
superagent
                // let options = {
        //     'method': 'post',
        //     'pfx': pfx,
        //     'passphrase': pay_conf.mchid,
        // };
        // options.agent = new https.Agent(options);
        // let result = await request.post(url)
        //     .send(xmlOption)
        //     .pfx({
        //         'pfx': pfx,
        //         'passphrase': pay_conf.mchid,
        //     })
        //     .key()
        //     // .set({
        //     //     'agent': options.agent,
        //     // })
        //     .type('xml')
        //     .then(data => {
        //         console.log(data.text);
        //     })
        //     .catch(err => {
        //         console.log(err);
        //     });

        已经不维护的request
                // require('request')({
        //     'method': 'post',
        //     'url': url,
        //     'body': xmlOption,
        //     'agentOptions': {
        //         'pfx': pfx,
        //         'passphrase': pay_conf.mchid,
        //     },
        // }, (err, res, body) => {
        //     console.log(res);
        // });

superagent 如何模拟html的表单上传文件
superagent
   .post('/upload')
   .field('user[name]', 'Tobi')
   .field('user[email]', 'tobi@learnboost.com')
   .field('friends[]', ['loki', 'jane'])
   .attach('image', 'path/to/tobi.png',{
           contentType: 'mime/type'
   })
   .then(callback);
