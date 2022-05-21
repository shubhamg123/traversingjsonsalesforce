# traversing  json  salesforce
Skip to content
olopsman/JSONcreateParser.cl
Apex sample JSON createParser
JSONcreateParser.cls
String input = '[{"_id":"1","name":"paulo","age":42, "car" : {"model" : "outlander", "year" : "2016"}, "kids" : ["penny", "padma", "amber", "pauline"]}, {"_id":"2","name":"tin","age":40, "car" : {"model" : "crv", "year" : "2004"},"kids" : ["mary", "sophie", "patrice", "laeticia"]}]';

JSONParser parser = JSON.createParser(input);
List<GetPerson> gpList = new List<GetPerson>();

while(parser.nextToken() != JSONToken.END_ARRAY) { // we started with an array of objects
	GetPerson gp = new GetPerson();
	while(parser.nextToken() != JSONToken.END_OBJECT){ // loop through each object
		if(parser.getCurrentToken() == JSONToken.FIELD_NAME) { //token should be field name
			String attr = parser.getText(); //get the text of the field name
			parser.nextToken(); // move the pointer
			//start mapping the fields
			if(attr == '_id') {
				gp.x_id = parser.getText();
			} else if(attr == 'name') {
				gp.name = parser.getText();
			} else if(attr == 'age') {
				gp.age = parser.getIntegerValue();
			} else if(attr == 'car') {
				//create instance of the inner class car
				GetPerson.Car gpcar = new GetPerson.Car();
				//this is new object - make another while to loop through
				while(parser.nextToken() != JSONToken.END_OBJECT) {
					if(parser.getCurrentToken() == JSONToken.FIELD_NAME) {
						String carAttr = parser.getText();
						//move the pointer
						parser.nextToken();
						if(carAttr == 'model') {
							gpcar.model = parser.getText();
						} else if(carAttr == 'year') {
							gpcar.year = parser.getText();
							//finally assign the year assign the car
							gp.car = gpcar;
						}
					}
				}
			} else if(attr == 'kids') { //this is array of values
				List<String> kids = new List<String>();
				//do another while
				while(parser.nextToken() != JSONToken.END_ARRAY) {
					kids.add(parser.getText());
				}
				gp.kids = kids;
			}
		}
	}
	gpList.add(gp);
}
system.assertEquals(gpList.size(), 2);

system.debug(gpList);

	
	anotyhr example
--------------------------------------------------------------------->
	String jsonInput = '{\n' +
    ' "description" :"An appliance",\n' +
    ' "accessories" : [ "powerCord", ' +
      '{ "right":"door handle1", ' +
        '"left":"door handle2" } ],\n' +
    ' "dimensions" : ' +
      '{ "height" : 5.5 , ' +
        '"width" : 3.0 , ' +
        '"depth" : 2.2 },\n' +
    ' "type" : null,\n' +
    ' "inventory" : 2000,\n' +
    ' "price" : 1023.45,\n' +
    ' "isShipped" : true,\n' +
    ' "modelNumber" : "123"\n' +
    '}';
   
Map<String, Object> m =
   (Map<String, Object>)
      JSON.deserializeUntyped(jsonInput);
system.debug(m);

list<object> x= (list<object>) m.get('accessories');
system.debug(x);
map<string,object> ds = (map<string,object>)x[1];
string s=(string)ds.get('left');
system.debug(s);
system.debug(ds);

    system.debug(x);
	
