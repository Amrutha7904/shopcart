# Shopping Cart REST API
A repository containing example of REST API in python with  flask  and  mongoDB, for CRUD operations on a cart.

In this repository, I will  explain my work on creating REST API in python with the help of flask, MongoDB to perform CRUD operations on shopping cart.<br>


# Problem Statement
To create a shopping cart REST API(Use Python/Mongodb) that handles CRUD operations for a specific user like create cart, get items, add items and remove items.

# Solution
## Tools  Required
- MongoDB : To create and maintain database
- Postman App : To make requests to API from client side
- Python(3.9.6) : To create the  API

## Download and Install Required  tools
- MongoDB Community Version (5.0.1) : https://www.mongodb.com/try/download/community
- Postman : https://www.postman.com/downloads/
- Python (3.9.6) : https://www.python.org/downloads/

## Required  packages for python
- flask : To work with flask framework in python
- pymongo : To work with MongoDB database from python

##### Command to  install required packages
- All  packages at once : pip install -r requirements.txt
- Individual packages : pip install <package_name> 
  - <package_name> : flask, py_mongo

## Operations that can be performed with API
- Create and/or Add items to  cart
- Get items in cart
- Update name for  item in  cart
- Update price for item in cart
- Update quantity for  item in cart
- Remove specific item from cart
- Empty cart

## Explaination about  different operations
- Create and/or  Add items to  cart :
  - By performing  this  operation, if the  collection/table doesn't  exist in MongoDB,  than it will  create the collection  and add item to  the  cart,  else if the collection already exists, then  it will add item to  existig cart.
  - EndPoint : localhost:5000/carts
  - Methods : POST
  - form_data : "name", "price",  "quantity"
- Get items in cart :
  - This operation allow  you to  get all the items present in the cart
  - EndPoint : localhost:5000/carts
  - Methods : GET
- Update name of item in cart
  - This  operation allow you to  update name of item present in the cart by providing item id.
  - EndPoint : localhost:5000/carts/name/{item_id}
  - Methods : PUT
  - form_data : "name"
- Update price of item in cart
  - This  operation allow you to  update price of item present in the cart by providing item id.
  - EndPoint : localhost:5000/carts/price/{item_id}
  - Methods : PUT
  - form_data : "price"
- Update quantity of item in cart
  - This  operation allow you to  update quantity of item present in the cart by providing item id.
  - EndPoint : localhost:5000/carts/quantity/{item_id}
  - Methods : PUT
  - form_data : "quantity"
- Remove specific item from cart
  - This operation  allows you to  remove specific item from cart by providing its item id.
  - EndPoint : localhost:5000/carts/{item_id}
  - Methods : DELETE
- Empty Cart
  - This operation  allows you to  empty your cart by  removing all  items in the cart.
  - EndPoint : localhost:5000/carts
  - Methods : DELETE

## Code with explaination
- Importing requied packages
```python
from flask import Flask, Response, request
import json
from bson.objectid import  ObjectId
import pymongo
```
- Starting the server
```python
app = Flask(__name__)
if (__name__ == "__main__"):
    app.run(port=5000, debug=True)
```
- Connecting with MongoDB
```python
try:
    mongo = pymongo.MongoClient(
        host="localhost",
        port=27017,
        serverSelectionTimeoutMS = 1000
    )
    mongo.server_info() ## trigger  exception if cannot connect to database
    db = mongo.miskaa    
except:
    print("ERROR - Cannot connect  to  database")
```
- Adding to  the cart
```python
@app.route("/carts", methods=["POST"])
def create_cart():
    try:
        item = {"name":request.form["name"],
                "price":request.form["price"],
                "quantity":request.form["quantity"]}
        dbResponse = db.carts.insert_one(item)
        print(dbResponse.inserted_id)
        return Response(
            response=json.dumps(
                {"message":"item added to cart",
                 "id":f"{dbResponse.inserted_id}"
                }),
            status=200,
            mimetype="application/json"
        )
        
    except Exception as ex:
        print("*****************")
        print(ex)
        print("*****************")
```
- Viewing the cart
```python
@app.route("/carts", methods=["GET"])
def show_items_in_cart():
    try:
        data = list(db.carts.find())
        print(data)
        for item in data:
            item["_id"] = str(item["_id"])
        return Response(
            response=json.dumps(data),
            status=200,
            mimetype="application/json"
        )
    except Exception as ex:
        print(ex)
        return Response(
            response=json.dumps({"message":"Error fetching items in cart"}),
            status=500,
            mimetype="application/json"
        )
```
- Updating name of  item from cart
```python
@app.route("/carts/name/<id>", methods=["PUT"])
def update_item_name(id):
    try:
        dbResponse = db.carts.update_one(
            {"_id":ObjectId(id)},
            {"$set":{"name":request.form["name"]}}
        )
        if (dbResponse.modified_count > 0):
            return Response(
                response=json.dumps({"message":"Item name updated"}),
                status=200,
                mimetype="application/json"
            )
        else:
            return Response(
                response=json.dumps({"message":"Nothing to Update"}),
                status=200,
                mimetype="application/json"
            )
    except Exception as ex:
        print("**********")
        print(ex)
        print("**********")
        return Response(
            response=json.dumps({"message":"Error updating items in cart"}),
            status=500,
            mimetype="application/json"
        )
```
- Updating  price of item from cart
```python
@app.route("/carts/price/<id>", methods=["PUT"])
def update_item_price(id):
    try:
        dbResponse = db.carts.update_one(
            {"_id":ObjectId(id)},
            {"$set":{"price":request.form["price"]}}
        )
        if (dbResponse.modified_count > 0):
            return Response(
                response=json.dumps({"message":"Item price updated"}),
                status=200,
                mimetype="application/json"
            )
        else:
            return Response(
                response=json.dumps({"message":"Nothing to Update"}),
                status=200,
                mimetype="application/json"
            )
    except Exception as ex:
        print("**********")
        print(ex)
        print("**********")
        return Response(
            response=json.dumps({"message":"Error updating items in cart"}),
            status=500,
            mimetype="application/json"
        )
```
- Updating quantity of item from cart
```python
@app.route("/carts/quantity/<id>", methods=["PUT"])
def update_item_quantity(id):
    try:
        dbResponse = db.carts.update_one(
            {"_id":ObjectId(id)},
            {"$set":{"quantity":request.form["quantity"]}}
        )
        if (dbResponse.modified_count > 0):
            return Response(
                response=json.dumps({"message":"Item quantity updated"}),
                status=200,
                mimetype="application/json"
            )
        else:
            return Response(
                response=json.dumps({"message":"Nothing to Update"}),
                status=200,
                mimetype="application/json"
            )
    except Exception as ex:
        print("**********")
        print(ex)
        print("**********")
        return Response(
            response=json.dumps({"message":"Error updating items in cart"}),
            status=500,
            mimetype="application/json"
        )
```
- Deleting item from cart
```python
@app.route("/carts/<id>", methods=["DELETE"])
def delete_item(id):
    try:
        dbResponse = db.carts.delete_one(
            {"_id":ObjectId(id)}
        )
        if (dbResponse.deleted_count > 0):
            return Response(
                response=json.dumps(
                    {"message":"item Deleted",
                    "id":f"{id}"}),
                status=200,
                mimetype="application/json"
            )
        else:
            return Response(
                response=json.dumps(
                    {"message":"item doesn't exist",
                    "id":f"{id}"}),
                status=200,
                mimetype="application/json"
            )
    except Exception as ex:
        print("**********")
        print(ex)
        print("**********")
        return Response(
            response=json.dumps({"message":"Error deleting items in cart"}),
            status=500,
            mimetype="application/json"
        )
```
- Emptying the cart
```python
@app.route("/carts", methods=["DELETE"])
def delete_cart():
    try:
        dbResponse = db.carts.delete_many({})
        if (dbResponse.deleted_count > 0):
            return Response(
                response=json.dumps(
                    {"message":"items Deleted",
                    "count":f"{dbResponse.deleted_count}"}),
                status=200,
                mimetype="application/json"
            )
        else:
            return Response(
                response=json.dumps(
                    {"message":"Cart is empty"}),
                status=200,
                mimetype="application/json"
            )
    except Exception as ex:
        print("**********")
        print(ex)
        print("**********")
        return Response(
            response=json.dumps({"message":"Error deleting items in cart"}),
            status=500,
            mimetype="application/json"
        )
```
Author : Amrutha<br>
Contact : amruthaos751@gmail.com