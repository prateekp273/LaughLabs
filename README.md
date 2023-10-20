# Meme App using API and Provider

### Splash Screen
<kbd><img src="https://user-images.githubusercontent.com/109909231/224924545-2479b895-9be3-4436-8401-10c25291e19a.png" width="250" height="550"></kbd>

### Home Screen&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;Adding Items&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;Cart Screen
<kbd><img src="https://user-images.githubusercontent.com/109909231/224924675-ddb5a35f-4b5a-4390-a4e2-316d9d92852e.png" width="250" height="550"></kbd>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<kbd><img src="https://user-images.githubusercontent.com/109909231/224924803-b9d3270b-e425-4efb-96dc-ec830d5db4f1.png" width="250" height="550"></kbd>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<kbd><img src="https://user-images.githubusercontent.com/109909231/224924817-24c40565-7207-4acf-940d-9eb90619ed0b.png" width="250" height="550"></kbd>


### Downloading Image&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;Empty Cart
<kbd><img src="https://user-images.githubusercontent.com/109909231/224925346-cb5bd3cd-c688-4053-a0c8-a56f549ee9ad.png" width="250" height="550"></kbd>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<kbd><img src="https://user-images.githubusercontent.com/109909231/224946487-ecf8b170-bec6-4b91-94ad-96ddbb500327.png" width="250" height="550"></kbd>

### Recording: 

https://user-images.githubusercontent.com/109909231/224942240-a3389d7f-3005-4151-a6e3-20d6f59483f0.mp4

## Packages used:
 - [http](https://pub.dev/packages/http)
 - [provider](https://pub.dev/packages/provider)
 - [dynamic_height_grid_view](https://pub.dev/packages/dynamic_height_grid_view)
 - [path](https://pub.dev/packages/path)
 - [path_provider](https://pub.dev/packages/path_provider)
___
## Detailed description of implementation 

### [i]. Fetching data from API and rendering UI
### [ii]. Provider for state management
### [iii]. Saving Image to Local Storage  
___
  
### [i]. Fetching data from API and rendering UI

#### Memes API : [MemesAPI](https://api.imgflip.com/get_memes) 
#### _Not sure How to parse JSON data from API?_ Detailed Explanation [here](https://github.com/AKR-2803/FlutterAPI-FlutterProvider#readme)

Code here : [home_page.dart](https://github.com/AKR-2803/MemeAppFlutter/blob/akr-branch/lib/screens/home_page.dart)

Parsing the data from memesAPI
```dart
Future<MemesModel> getMemesApi() async {
  final response =
      await http.get(Uri.parse("https://api.imgflip.com/get_memes"));
  var data = jsonDecode(response.body);
  if (response.statusCode == 200) {
    return MemesModel.fromJson(data);
  } else {
    return MemesModel.fromJson(data);
  }
}
```
- memes is of type Future<MemesModel> which will contain the fetched data.
- Note : late keyword is important.
- call the method in initState()
```dart
class _HomePageState extends State<HomePage> {
  late Future<MemesModel> memes;

  @override
  void initState() {
    super.initState();
    memes = getMemesApi();
  }
```

- use FutureBuilder to render the data on screen

```dart
body: FutureBuilder<MemesModel>(
        future: memes,
        builder: (context, snapshot) {
```

- I used DynamicHeightGridView (package [here](https://pub.dev/packages/dynamic_height_grid_view)), you may use any preferred widget/package that suits you.
- make sure you give itemCount property
- snapshot will either have data or error (```snapshot.hasData``` , ```snapshot.harError```)
- if ```snapshot.hasData``` render the UI, else if ```snapshot.hasError``` display the error, else render a CircularProgressIndicator, i.e while waiting for data to be fetched.

```dart
DynamicHeightGridView(
                  crossAxisCount: 2,
                  mainAxisSpacing: 10,
                  crossAxisSpacing: 25,
                  itemCount:
                      //nullcheck operator
                      snapshot.hasData ? snapshot.data!.data!.memes!.length : 1,
                  builder: (context, index) {
                  
                 //data fetched successfully
                    if (snapshot.hasData) {
                      //render UI here
                    }
```
```dart
//data couldn't be fetched due to some error
else if (snapshot.hasError) {
                      return Center(
                        child: Text("Error Occured : ${snapshot.error}"),
                      );
```
```dart
//waiting for data to be fetched (just the way youtube videos show circular progressor while buffering)
else {
                      return const Center(
                          child: CircularProgressIndicator(
                        color: Colors.teal,
                      ));
                    }
```
___

### [ii]. Provider for state management

- We've rendered the UI. Great!
- But, think about the cart feature, whenever the "Get This Meme" button is tapped :
- how will the we increment the counter value on the top right corner on the cart icon?
- how will we render the UI accordingly in the cart page?
#### That's where PROVIDER comes in!

Provider is a state management tool, you can refer more about it here : [ProviderExample](https://docs.flutter.dev/development/data-and-backend/state-mgmt/simple)

Note : It may take sometime to understand provider, my case was no different. (Remember : Nothing comes overnight, good things take time!)

- Once/If you are familiar with provider, lets move ahead.

We have 2 basic requirements using provider.

Case-1 : Increment/Decreament the counter value on the cart icon on home page(refer images above).

Case-2 : Building the Cart page.
___

Case-1 : Increment/Decreament Counter
Code here : [cart_counter_provider.dart](https://github.com/AKR-2803/MemeAppFlutter/blob/akr-branch/lib/providers/cart_counter_provider.dart)
- define the Provider class
- make sure to extend ChangeNotifier
- declare an integer cartCount
- define the constructor(initalize cartCount value) and getter method
- define the methods increment() and decreament()
- notifyListeners()


