# openpay

Provides a ruby api to the Openpay Rest api.

## Full documentation at: http://docs.openpay.mx/

## Installation

   Add the following line to your Gem file


   # gem install --source https://code.stripe.com stripe

    gem 'openpay'

Update your bundle:

    $ bundle

Or install it from the command line:

    $ gem install openpay

##Requirements

    * ruby 1.8.7 or higher

## Usage:


### Initialization:
```ruby
require 'openpay'

#merchant and private key
merchant_id='mywvupjjs9xdnryxtplq'
private_key='sk_92b25d3baec149e6b428d81abfe37006'


#An openpay resource factory is created pointing  to the development environment
openpay=OpenpayApi.new(merchant_id,private_key)

#To enable production mode you should pass a third argument as true
#openpay_prod=OpenPayApi.new(merchant_id,private_key,true)

 ```



### The openpay factory instance is in charge to generate the required resources.
Each rest resource is represented by a class.Resource classes should be initialized using the factory method as described below.

 ```ruby
#creating a instance for each available resource
bankaccounts=openpay.create(:bankaccounts)
cards=openpay.create(:cards)
charges=openpay.create(:charges)
customers=openpay.create(:customers)
fees=openpay.create(:fees)
payouts=openpay.create(:payouts)
plans=openpay.create(:plans)
subscriptions=openpay.create(:subscriptions)
transfers=openpay.create(:transfers)
```




### ruby hashes are used as the message format

```ruby
require 'pp'

pp card_hash
{:bank_name=>"visa",
:holder_name=>"Vicente Olmos",
:expiration_month=>"09",
:card_number=>"4111111111111111",
:expiration_year=>"14",
:bank_code=>"bmx",
:cvv2=>"111",
:address=>
{:postal_code=>"76190",
:state=>"QRO",
:line1=>"LINE1",
:line2=>"LINE2",
:line3=>"LINE3",
:country_code=>"MX",
:city=>"Queretaro"}}
```


Below  an example of how you can create a merchant card, a hash is passed as an argument as well a hash is returned as part of the response.

```ruby
cards=openpay.create(:cards)

#creates merchant card
response_hash=cards.create(card_hash)
```

If you want to use json instead, you can perform the transform prior the api call  as noted below:
 ```ruby
cards_json='{"bank_name":"visa","holder_name":"Vicente Olmos","expiration_month":"09",
"card_number":"4111111111111111","expiration_year":"14","bank_code":"bmx","cvv2":"111",
"address":{"postal_code":"76190","state":"QRO","line1":"LINE1","line2":"LINE2","line3":"LINE3","country_code":"MX","city":"Queretaro"}}'

cards.create(JSON[cards_json])
```

The same way, you can perform a transform after the api call.
 ```ruby
response_json=cards.create(JSON[cards_json]).to_json
```

#### Implementation
 Each resources depending its structure and available methods will have one or more of the following methods:


##### Arguments
Given most resources  belong either to merchant or a customer,m ost api methods have two arguments
The first argument represent the json/hash object, while the second argument which is optional represents the customer_id .
So if the just one argument is provided the action will be performed at the merchant level,
but if the second argument is provided passing the customer_id it will be performed at the customer level


 ```ruby
#Merchant
open_pay_resource.create(object_id)

#Customer
open_pay_resource.create(object_id,customer_id)
 ```

#####  Methods

#####create

   Creates the given resource
 ```ruby
     open_pay_resource.create(object_id,customer_id=nil)
 ```


#####get

   Gets an instance of a  given resource

```ruby
open_pay_resource.get(object_id,customer_id=nil)
```


#####delete

  Deletes an instance o


```ruby
open_pay_resource.delete(object_id,customer_id=nil)
```


#####delete_all

   Borra todas las instancia de un recurso (disponible solo en algunos metodos y en ambiente de pruebas)


```ruby
open_pay_resource.delete_all(customer_id=nil)
```


#####all
   Regresa un Array con todas  las  instancia de un recurso
```ruby
open_pay_resource.all(customer_id=nil)
```

#####each
   Regresa un bloque con todas  las  instancia de un recurso
```ruby
open_pay_resource.each(customer_id=nil)
 ```


## Exceptions/Errors

#### Este Api utiliza como base la libreria de rest-client[1][2]
Por lo cual hemos decidido utilizar su sistema de exepciones tal cual es.

Para codigos de regreso del 200 al 207, una excepcion de tipo  RestClient::Response sera regresada.

Para codigos de regreso 301, 302 o 307, se hara un redirecionamiento si la peticion de GET o HEAD.

Para el codigo 303, se hara un redirecionamiento y el request sera transformado  en un GET.

 Para otros casos una RestClient::Exception con la Respuesta sera lanzado; Una expcion expecifica sera lanzada para errores conocidos.

       #En el caso de listar un objeto no existente una excepcion de tipo RestClient::ResourceNotFound sera lanzada

      expect { @cards.all('111111') } .to raise_exception   RestClient::ResourceNotFound

Al generarse una exepcion se genera tambien un warning, si tienes acceso a la consola, podras ver ahi tus mensajes de errror en forma de warnings


[1] https://github.com/rest-client/rest-client

[2] http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html




## More information

For more use cases take a look at the test/spec folder

1.  http://docs.openpay.mx/
