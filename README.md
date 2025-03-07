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

🧸💬 Communication allows time to invoke execution when the first design allows time taken process into web service but we found some issues when increasing thought to the synchronous method request-response from different threads reply on the same treads but the product application support insists not their application mistake then we try to disable caches and moving to async-process. Work experience with synchronized processes never had this problem but we do it parallel to the application support working on the case. </br>
🐑💬 ➰ The API is registered by multiple threads of working web service methods and we discovered that working with the asynchronize method does not find the same issue or they are different web service APIs, we use SANIC for this time. We create application routing points and separate code files as security concerns one romance we put into the separate code files is they include as less API or components as routing files by separate database service and API service as well. </br>

🦭💬 The API does not support the await function ( return as dictionary and not necessary too ) since they are responses in real-time and asynchronous is implemented for client invoke web services methods.  </br>
🦭💬 The leading of the group is the target and the second is navigate where we input the parameter the target is answered with information and data extraction to consume and route our robot to the correct order destination. </br>

🐯💬 Culture-INFO, an asynchronous method should have the key to identify the same message communication trails because of other network performance required to use to same information, not including intellective intelligence routing. </br>
🦁💬 That is the concept of UUID where it can be any number or series present of network stream that can be used among devices to prevent pattern recognitions and information leaks or they can help network equipment to categorize the message priority </br>  

## Codings example
🐐💬 Routing page that saved several import libraries but routed to multiple websites in the endpoints, since there is no shared variable by replace of text library or print function will not see anything important or they may not extract information from the class of end-points not easy ( see only its input or empty information ). External - internal function locked. </br> 
```
# src/server.py
from sanic import Sanic, text

import asyncio

import order_endpoint;
import order_endpoint2;
import order_endpoint3;
import order_endpointlimit;

new_ordermodel = order_endpoint.OrderModel("Main");
new_ordermodel2 = order_endpoint2.OrderModel2("Main");
new_ordermodel3 = order_endpoint3.OrderModel3("Main");
new_ordermodellimit = order_endpointlimit.OrderModelLimit("Main");

app = Sanic("app")
new_ordermodel.get_order_by_client_order_id_route(app)
new_ordermodel.createorder_route(app)
new_ordermodel.deleteorder_route(app)
new_ordermodel2.get_order_by_order_id_route(app)
new_ordermodel2.updatetakeprofit_route(app)
new_ordermodel3.updateorderqty_route(app)
new_ordermodellimit.createorderlimit_route(app)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```
👧💬 🎈 In the class of execution you can create a program and import more libraries for work specifications, the ```re``` library is my favorite because of it is common to input for none-SQL databases too and shared among functions is only a text string, and saved to create small handling function that use one or twice. <br>
👧💬 🎈 You still see some split functions that may because of the time escape and it can transform into regular expression ```re``` for standardization.<br>
```
import os;
import re;

from sanic import response, text
from bson.json_util import dumps;
import alpaca_trade_api as tradeapi
import json
import uuid
import MongoDBDatabase;

from dotenv import load_dotenv
import secret_service


class OrderModel(object):

    def __init__(self, comingFrom):
        super().__init__();
        self.name = "OrderModel";        
        self.current_order = None;
        self.position = 0;
        self.mongoDB = MongoDBDatabase.MongoDBDatabase();

        self.read_configuation()
        self.api = tradeapi.REST(self.ALPACA_API_KEY, self.ALPACA_SECRET_KEY, self.APCA_API_DATA_URL);
        self.api2 = tradeapi.REST(self.ALPACA_API_KEY2, self.ALPACA_SECRET_KEY2, self.APCA_API_DATA_URL2);

        return;

    def read_configuation(self):

        load_dotenv()
        self.APCA_API_DATA_URL = os.getenv('APCA_API_DATA_URL_1')
        self.ALPACA_API_KEY = os.getenv('ALPACA_API_KEY_1')
        self.ALPACA_SECRET_KEY = os.getenv('ALPACA_SECRET_KEY_1')

        self.TRADE_TYPE = os.getenv('TRADE_TYPE_2')
        self.APCA_API_DATA_URL2 = os.getenv('APCA_API_DATA_URL_2')

        (secrete_key, passkey) = secret_service.get_credentials(trade_type=self.TRADE_TYPE);
        self.ALPACA_API_KEY2 = secrete_key
        self.ALPACA_SECRET_KEY2 = passkey
        return
        
    def __str__ ():
        return "OrderModel";

    def register_torobotaccountAPIKEY(self , robotname):
        resultset = self.mongoDB.find_alpacakeyfromDB(robotname, "system admin");
        
        if resultset :
            _accountname = resultset["accountname"];
            self.ALPACA_API_KEY = resultset["ALPACA_API_KEY"];
            self.ALPACA_SECRET_KEY = resultset["ALPACA_SECRET_KEY"];
            self.APCA_API_DATA_URL = "https://" + resultset["APCA_API_DATA_URL"];
            self.api = tradeapi.REST(self.ALPACA_API_KEY, self.ALPACA_SECRET_KEY, self.APCA_API_DATA_URL, api_version='v2');

            return self.api
        else :
            load_dotenv()
            self.APCA_API_DATA_URL = os.getenv('APCA_API_DATA_URL_1')
            self.ALPACA_API_KEY = os.getenv('ALPACA_API_KEY_1')
            self.ALPACA_SECRET_KEY = os.getenv('ALPACA_SECRET_KEY_1')
            self.api = tradeapi.REST(self.ALPACA_API_KEY, self.ALPACA_SECRET_KEY, self.APCA_API_DATA_URL, api_version='v2');

            return self.api;

    async def get_order_by_client_order_id(self, request):
        data = request.query_string

        ###
        unique_id = data.split("/")[0];
        robotname = data.split("/")[1];
        trade_type = data.split("/")[2];

        if trade_type == "live" :
            self.api = self.api2;
        else :
            self.read_configuation();
            self.api = self.register_torobotaccountAPIKEY(robotname);

        response = [];
        try:
            response.append( json.loads(dumps(self.api.get_order_by_client_order_id( unique_id )._raw)) );
        except Exception as error:
            response = json.loads("{ \"message\": \"{error}}\" }".replace("{error}", repr(error)));
        ###


        return text(dumps(response))

    def get_order_by_client_order_id_route(self, app):
        app.add_route(self.get_order_by_client_order_id, '/getorder_byclientorderid/', methods=['GET'])


    async def deleteorder(self, request):
        # '/deleteOrder/{order_id}/{robotname}/{trade_type}'

        data = request.query_string
        order_id = data.split("/")[0];
        robotname = data.split("/")[1];
        trade_type = data.split("/")[2];

        # counting number of requests
        self.mongoDB.increase_requestcounter(robotname, trade_type);
        #

        if trade_type == "live" :
            self.api = self.api2;
        else :
            self.read_configuation();
            self.api = self.register_torobotaccountAPIKEY(robotname);

        response = [];
        result = "";
        message = "";
        unique_id = None;
        requester = "system admin"


        try :
            response.append(self.api.cancel_order(order_id))
            result = "success";
            message = "delete order success " + order_id;

            # resp.text = dumps(response);
            # resp.data = dumps(response);

        

        except Exception as ex:
            message = ex;
            result = "fail";
        finally:
            self.mongoDB.insert_resultstoDB(result, message, robotname, trade_type); 

        response = [];

        try :
            response.append( json.loads(dumps(self.api.get_order( order_id )._raw)) );
        except Exception as error:
            response = json.loads("{ \"message\": \"{error}}\" }".replace("{error}", repr(error)));

        return text(dumps(response));

    def deleteorder_route(self, app):
        app.add_route(self.deleteorder, '/deleteorderbyid/', methods=['DELETE'])

    async def createorder(self, request):

        # '/createOrder/{symbol}/{side}/{qty}/{price}/{type}/{robotname}/{trade_type}'

        data = request.query_string
        symbol = data.split("/")[0];
        side = data.split("/")[1];
        qty = data.split("/")[2];
        price = data.split("/")[3];
        type = data.split("/")[4];
        robotname = data.split("/")[5];
        trade_type = data.split("/")[6];

        result, message = None, None;

        # counting number of requests
        self.mongoDB.increase_requestcounter(robotname, trade_type);
        #

        if trade_type == "live" :
            self.api = self.api2;
        else :
            self.read_configuation();
            self.api = self.register_torobotaccountAPIKEY(robotname);

        requester = "system admin";
        unique_id = robotname + "_" + str(uuid.uuid4());

        ###########################################################################################################################################################
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
                str_message = json.loads('{ "message" : "need to be verified {robotname}" }'.replace("{robotname}", robotname))
                return text(dumps(str_message));

            pass;
        else :
            str_message = json.loads('{ "message" : "error: not found robotname {robotname}" }'.replace("{robotname}", robotname))
            return text(dumps(str_message));
        ###
        ###########################################################################################################################################################

        ### add logic to control amount to buy for robot
        # {'c': ['@'], 'i': 2712, 'p': 253.48, 's': 100, 't': '2024-12-18T15:47:21.544832162Z', 'x': 'V', 'z': 'C'}
        _temp = json.loads(dumps(self.api.get_latest_trade( symbol )._raw));
        _currentprice = float(_temp["p"]);
        _targetamount = _currentprice * int(qty);

        ### lookup robot amount of money
        listof_result = json.loads(dumps(self.mongoDB.find_robotnamefromDB(robotname, requester, trade_type)));
        amount= float(listof_result["amount"]);

        ### logics
        if amount < _targetamount and side == "buy" :
            str_message = json.loads('{ "message" : "{amount} amount is not enough and currently has {current}" }'.replace('{amount}', str(_targetamount)).replace('{current}', str(amount)))
            return text(dumps(str_message));
        ###########################################################################################################################################################


        try:
            if type == "limit" :
                self.current_order = self.api.submit_order(
                    symbol, qty, side,
                    'limit', 'day', price, client_order_id=unique_id
                )._raw
            elif type == "market":
                self.current_order = self.api.submit_order(
                    symbol, qty, side,
                    'market', 'day', client_order_id=unique_id
                )._raw
            else :
                str_message = json.loads('{ "message" : "error: invalid order type" }');
                return text(dumps(str_message));

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
                listof_result = json.loads(dumps(self.mongoDB.insert_robotstoDB(_robotname, _accountname, _username, _password, _amount, requester, trade_type)));
            elif side == "sell" :
                new_amount = amount + (_currentprice * float(qty));            
                _robotname = robotname;
                _accountname = listof_result["accountname"];
                _username = listof_result["username"];
                _password = listof_result["password"];
                _amount = new_amount;
                requester = "system admin"
                listof_result = json.loads(dumps(self.mongoDB.insert_robotstoDB(_robotname, _accountname, _username, _password, _amount, requester, trade_type)));
            ###

            ### add order_id to database records
            _temp = json.loads(dumps(self.current_order));
            order_id = _temp["id"];
            client_order_id = _temp["client_order_id"];
            status = _temp["status"];

            result = self.mongoDB.insert_orderstoDB( symbol, side, qty, price, robotname, unique_id, order_id, requester, -1, -1, status, trade_type );
            result = "success";
            
            str_message = dumps(_temp);
            message = "create order success " + side + " " + symbol + " " + qty;

            self.mongoDB.insert_filledupdatetoDB(robotname, order_id, unique_id, status, requester, trade_type);
        except Exception as error:
            str_message = json.loads("{ \"message\" : \"{msg}\"}".replace("{msg}", str(error)));
            result = "fail";
            message = error;
            return text(dumps(str_message));
        finally:
            self.mongoDB.insert_resultstoDB(result, message, robotname, trade_type); 



        return text(dumps(self.current_order));

    def createorder_route(self, app):
        app.add_route(self.createorder, '/createOrder/', methods=['POST'])
```

---

<p align="center" width="100%">
    <img width="30%" src="https://github.com/jkaewprateep/advanced_mysql_topics_notes/blob/main/custom_dataset.png">
    <img width="30%" src="https://github.com/jkaewprateep/advanced_mysql_topics_notes/blob/main/custom_dataset_2.png"> </br>
    <b> 🥺💬 รับจ้างเขียน functions </b> </br>
</p
