# async_webservice
Asynchronous Python web service

<p align="center" width="100%">
    <img width="30%" src="https://github.com/jkaewprateep/Proxyserver_in_python/blob/main/python-logo.jpg">
    <img width="25%" src="https://github.com/jkaewprateep/async_webservice/blob/main/async.jpg">
    <img width="10%" src="https://github.com/jkaewprateep/async_webservice/blob/main/image22.jpg">
    <img width="9%" src="https://github.com/jkaewprateep/async_webservice/blob/main/08419ff9-9066-4114-9af4-cca209abc322.jpg"> </br>
    <b> Asynchronous web service in Python programing language </b> </br>
    <b> ( Picture from Internet ) </b> </br>
</p>

```
async def on_post(self, req, resp, symbol, side, qty, price, type, robotname, takeprofit, stop_price, trade_type):
        
        # counting number of requests
        self.mongoDB.increase_requestcounter(robotname);
        #

        if trade_type == "live" :
            self.api = self.api2;
        else :
            self.read_configuation();
            self.api = self.register_torobotaccountAPIKEY(robotname);

        ### addedd verify robotname
        regx = re.compile("^robot.", re.IGNORECASE)

        item_1 = {
            "email" : regx
        };

        dbname = self.mongoDB.get_database2();
        collection_name = dbname["accounts"];
        resultset = collection_name.find(item_1);

        listof_robots = [];
        listof_isdemo = [];
        listof_isverified = [];
        for item in resultset :
            listof_robots.append(item["email"][item["email"].index(".") + 1:item["email"].index("@")]);
            listof_isdemo.append(item["isDemo"]);
            listof_isverified.append(item["isVerified"]);
            # print(item["email"][item["email"].index(".") + 1:item["email"].index("@")])

        idx_robotname = -1;
        try :    
            idx_robotname = listof_robots.index(robotname);
        except :        
            pass
        
        if idx_robotname > -1 :
            current_isdemo = listof_isdemo[idx_robotname];
            current_isverified = listof_isverified[idx_robotname];
            
            if not current_isverified :
                str_message = dumps('{ "message" : "need to be verified {robotname}" }'.replace("{robotname}", robotname))
                resp.text = str_message;
                resp.data = str_message;
                return

            pass;
        else :
            str_message = dumps('{ "message" : "error: not found robotname {robotname}" }'.replace("{robotname}", robotname))
            resp.text = str_message;
            resp.data = str_message;
            return
        ###

        self.symbol = symbol;
        self.last_price = price;
        requester = "system admin";
        unique_id = robotname + "_" + str(uuid.uuid4());

        result = "";
        message = "";

        ### add logic to control amount to buy for robot
        # {'c': ['@'], 'i': 2712, 'p': 253.48, 's': 100, 't': '2024-12-18T15:47:21.544832162Z', 'x': 'V', 'z': 'C'}
        _temp = json.loads(dumps(self.api.get_latest_trade( symbol )._raw));
        _currentprice = float(_temp["p"]);
        _targetamount = _currentprice * int(qty);

        ### lookup robot amount of money
        listof_result = json.loads(dumps(self.mongoDB.find_robotnamefromDB(robotname, requester)));
        amount= float(listof_result["amount"]);

        ### logics
        if amount < _targetamount :
            str_message = dumps('{ "message" : "{amount} amount is not enough and currently has {current}" }'.replace('{amount}', str(_targetamount)).replace('{current}', str(amount)))
            resp.text = str_message;
            resp.data = str_message;
            return;

        try:
            if type == "limit" :
                self.current_order = await self.api.submit_order(
                    self.symbol,
                    qty,
                    side,
                    "limit",
                    'day', self.last_price, client_order_id=unique_id,
                    # limit_price=limit_price_1,
                    # time_in_force="gtc",
                    order_class="bracket",
                    take_profit={"limit_price": str(takeprofit)},
                    stop_loss={"stop_price": str(stop_price)}
                )._raw

            elif type == "market":
                self.current_order = await self.api.submit_order(
                    self.symbol,
                    qty,
                    side,
                    "market",
                    client_order_id=unique_id,
                    # limit_price=limit_price_1,
                    time_in_force="gtc",
                    order_class="bracket",
                    take_profit={"limit_price": str(takeprofit)},
                    stop_loss={"stop_price": str(stop_price)}
                )._raw
            else :
                str_message = dumps('{ "message" : "error: invalid order type" }');
                resp.text = str_message;
                resp.data = str_message;
                return;

            ###
            ### remove number buy items ###
            if side == "buy" :
                new_amount = amount - _targetamount;
                _robotname = robotname;
                _accountname = listof_result["accountname"];
                _username = listof_result["username"];
                _password = listof_result["password"];
                _amount = new_amount;
                requester = "system admin"
                listof_result = json.loads(dumps(self.mongoDB.insert_robotstoDB(_robotname, _accountname, _username, _password, _amount, requester)));
            elif side == "sell" :
                new_amount = amount + (_currentprice * float(qty));            
                _robotname = robotname;
                _accountname = listof_result["accountname"];
                _username = listof_result["username"];
                _password = listof_result["password"];
                _amount = new_amount;
                requester = "system admin"
                listof_result = json.loads(dumps(self.mongoDB.insert_robotstoDB(_robotname, _accountname, _username, _password, _amount, requester)));
            ###


            resp.text = dumps(self.current_order);
            resp.data = dumps(self.current_order);

            ### add order_id to database records
            _temp = json.loads(dumps(self.current_order));
            order_id = _temp["id"];
            client_order_id = _temp["client_order_id"];
            status = _temp["status"];

            result = self.mongoDB.insert_orderstoDB( symbol, side, qty, price, robotname, unique_id, order_id, requester, price, stop_price, status );
            str_message = dumps(_temp);
            resp.text = str_message;
            resp.data = str_message;
            result = "success";
            message = "create orderlimit success " + symbol + " " + qty;

            # self.api.close();
            self.mongoDB.insert_filledupdatetoDB(robotname, order_id, unique_id, status, requester);
        except Exception as error:
            str_message = dumps('{ "message" : "error: {error}" }'.replace('{error}', str(error)))
            resp.text = str_message;
            resp.data = str_message;
            result = "fail";
            message = error;
            return;
        finally:
            self.mongoDB.insert_resultstoDB(result, message, robotname); 

        ###

        

        return;
```
