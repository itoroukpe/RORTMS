# RORTMS
### **How To Install Ruby on Rails with rbenv on Ubuntu 20.04**
https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-20-04#step-4-installing-rails
Prerequisites
To follow this tutorial, you need:

One Ubuntu 20.04 server set up by following the Ubuntu 20.04 initial server setup guide, including a sudo non-root user and a firewall.
Node.js installed using the official PPA, as explained in option 2 of How To Install Node.js on Ubuntu 20.04. A few Rails features, such as the Asset Pipeline, depend on a JavaScript Runtime. Node.js provides this functionality.

---
Step 1 – Install rbenv and Dependencies
Ruby relies on several packages that you can install through your package manager. Once those are installed, you can install rbenv and use it to install Ruby.

First, update your package list:
```
sudo apt update
```
Next, install the dependencies required to install Ruby:

```
sudo apt install git curl libssl-dev libreadline-dev zlib1g-dev autoconf bison build-essential libyaml-dev libreadline-dev libncurses5-dev libffi-dev libgdbm-dev
```
After installing the dependencies, you can install rbenv itself. Use curl to fetch the install script from Github and pipe it directly to bash to run the installer:
```
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
```
Next, add ~/.rbenv/bin to your $PATH so that you can use the rbenv command line utility. Do this by altering your ~/.bashrc file so that it affects future login sessions:
```
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
```
Then, add the command eval "$(rbenv init -)" to your ~/.bashrc file so rbenv loads automatically:
```
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
```
Next, apply the changes you made to your ~/.bashrc file to your current shell session:
```
source ~/.bashrc
```
Verify that rbenv is set up properly by running the type command, which will display more information about the rbenv command:
```
type rbenv
```
Your terminal window will display the following:
```
Output
rbenv is a function
rbenv ()
{
    local command;
    command="${1:-}";
    if [ "$#" -gt 0 ]; then
        shift;
    fi;
    case "$command" in
        rehash | shell)
            eval "$(rbenv "sh-$command" "$@")"
        ;;
        *)
            command rbenv "$command" "$@"
        ;;
    esac
}
```
At this point, you have both rbenv and ruby-build installed. Let’s install Ruby next.
---
### **Step 2 – Installing Ruby with ruby-build**
With the ruby-build plugin now installed, you can install whatever versions of Ruby that you may need with a single command. First, list all the available versions of Ruby:
```
rbenv install -l
```
The output of that command will be a list of versions that you can choose to install:
```
Output
2.6.8
2.7.4
3.0.2
jruby-9.2.19.0
mruby-3.0.0
rbx-5.0
truffleruby-21.2.0.1
truffleruby+graalvm-21.2.0

Only latest stable releases for each Ruby implementation are shown.
Use 'rbenv install --list-all / -L' to show all local versions.
```
Now let’s install Ruby 2.7.6:
```
rbenv install 2.7.6
```
Installing Ruby can be a lengthy process, so be prepared for the installation to take some time to complete.

Once it’s done installing, set it as your default version of Ruby with the global sub-command:
```
rbenv global 2.7.6
```
Verify that Ruby was properly installed by checking its version number:
```
ruby -v
```
If you installed version 2.7.6 of Ruby, this command will return output like this:
```
Output
ruby 3.0.2p107 (2021-07-07 revision 0db68f0233) [x86_64-linux]
```
---
### **Step 3 – Working with Gems**
Gems are the way Ruby libraries are distributed. You use the gem command to manage these gems, and use this command to install Rails.

When you install a gem, the installation process generates local documentation. This can add a significant amount of time to each gem’s installation process, so turn off local documentation generation by creating a file called ~/.gemrc which contains a configuration setting to turn off this feature:
```
echo "gem: --no-document" > ~/.gemrc
```
Bundler is a tool that manages gem dependencies for projects. Install the Bundler gem next, as Rails depends on it:
```
gem install bundler
```
You’ll receive the following output:
```
Output
Fetching bundler-2.2.27.gem
Successfully installed bundler-2.2.27
1 gem installed
```
You can use the gem env command (the subcommand env is short for environment) to learn more about the environment and configuration of gems. You can confirm where gems are being installed by using the home argument, like this:
```
gem env home
```
You’ll receive an output similar to this:
```
Output
/home/sammy/.rbenv/versions/2.7.6/lib/ruby/gems/2.7.0
```
Once you have gems set up, you can install Rails.
---
###**Step 4 – Installing Rails**
To install Rails, use the gem install command along with the -v flag to specify the version. For this tutorial, you’ll use version 6.1.4.1:
```
gem install rails -v 6.1.4.1
```
The gem command installs the gem you specify, as well as any of its dependencies. Rails is a complex web development framework and has many dependencies, so the process will take some time to complete. Eventually, you’ll receive a message stating that Rails is installed, along with its dependencies:
```
Output
...
Successfully installed rails-6.1.4.1
37 gems installed
```
Note: If you would like to install a different version of Rails, you can list the valid versions of Rails by doing a search, which will output a list of possible versions. You can then install a specific version, such as 4.2.7:
```
gem search '^rails$' --all
gem install rails -v 4.2.7
```
If you would like to install the latest version of Rails, run the command without a version specified:
```
gem install rails
```
rbenv works by creating a directory of shims, which point to the files used by the Ruby version that’s currently enabled. Through the rehash sub-command, rbenv maintains shims in that directory to match every Ruby command across every installed version of Ruby on your server. Whenever you install a new version of Ruby or a gem that provides commands as Rails does, you should run the following:
```
rbenv rehash
```
Verify that Rails has been installed properly by printing its version, with the following command:
```
rails -v
```
If it’s installed properly, this command will return the version of Rails that was installed:
```
Output
Rails 6.1.4.1
```












### ** Ruby on Rails Project: Transportation Management System (TMS) API for a Mobile App**  

This project demonstrates a **Ruby on Rails backend API** for a **Transportation Management System (TMS)** that serves a **mobile application** (React Native, Flutter, or any frontend). The API will manage **vehicles, drivers, trips, and geolocation tracking**.

---

### **📌 Step 1: Initialize the Rails Project**  
Run the following command to create a new Rails API project:  
```bash
rails new tms_api --api -d postgresql
```
Move into the project directory:  
```bash
cd tms_api
```
Set up the database:  
```bash
rails db:create
```

---

### **📌 Step 2: Generate Models & Migrations**  
We’ll create models for **Users (Drivers/Admins), Vehicles, and Trips**.

#### **1. User Model (For Drivers & Admins)**
```bash
rails generate model User name:string email:string role:string password_digest:string
```
Edit `app/models/user.rb`:
```ruby
class User < ApplicationRecord
  has_secure_password # Enables password hashing
  validates :email, uniqueness: true, presence: true
  has_many :vehicles
end
```

#### **2. Vehicle Model**
```bash
rails generate model Vehicle plate_number:string model:string status:string user:references
```
Edit `app/models/vehicle.rb`:
```ruby
class Vehicle < ApplicationRecord
  belongs_to :user
  has_many :trips
  validates :plate_number, uniqueness: true
end
```

#### **3. Trip Model**
```bash
rails generate model Trip origin:string destination:string status:string vehicle:references user:references start_time:datetime end_time:datetime
```
Edit `app/models/trip.rb`:
```ruby
class Trip < ApplicationRecord
  belongs_to :vehicle
  belongs_to :user
  validates :status, inclusion: { in: %w[pending active completed cancelled] }
end
```

Run the migrations:  
```bash
rails db:migrate
```

---

### **📌 Step 3: Implement Controllers & Routes**  
Create controllers for **Users, Vehicles, and Trips**.

#### **1. User Authentication & Management**
```bash
rails generate controller Users
```
Edit `app/controllers/users_controller.rb`:
```ruby
class UsersController < ApplicationController
  before_action :authorize_request, except: :create

  def create
    user = User.new(user_params)
    if user.save
      token = encode_token(user_id: user.id)
      render json: { user: user, token: token }, status: :created
    else
      render json: { errors: user.errors.full_messages }, status: :unprocessable_entity
    end
  end

  def show
    user = User.find(params[:id])
    render json: user
  end

  private

  def user_params
    params.permit(:name, :email, :password, :password_confirmation, :role)
  end
end
```

#### **2. Vehicle Management**
```bash
rails generate controller Vehicles
```
Edit `app/controllers/vehicles_controller.rb`:
```ruby
class VehiclesController < ApplicationController
  before_action :authorize_request

  def index
    vehicles = Vehicle.where(user_id: @current_user.id)
    render json: vehicles
  end

  def create
    vehicle = @current_user.vehicles.build(vehicle_params)
    if vehicle.save
      render json: vehicle, status: :created
    else
      render json: { errors: vehicle.errors.full_messages }, status: :unprocessable_entity
    end
  end

  private

  def vehicle_params
    params.permit(:plate_number, :model, :status)
  end
end
```

#### **3. Trip Management**
```bash
rails generate controller Trips
```
Edit `app/controllers/trips_controller.rb`:
```ruby
class TripsController < ApplicationController
  before_action :authorize_request

  def index
    trips = @current_user.trips
    render json: trips
  end

  def create
    trip = Trip.new(trip_params)
    trip.user = @current_user
    if trip.save
      render json: trip, status: :created
    else
      render json: { errors: trip.errors.full_messages }, status: :unprocessable_entity
    end
  end

  private

  def trip_params
    params.permit(:origin, :destination, :vehicle_id, :status, :start_time, :end_time)
  end
end
```

---

### **📌 Step 4: Configure Routes**
Edit `config/routes.rb`:
```ruby
Rails.application.routes.draw do
  post 'signup', to: 'users#create'
  post 'login', to: 'authentication#login'

  resources :users, only: [:show]
  resources :vehicles, only: [:index, :create]
  resources :trips, only: [:index, :create]
end
```

---

### **📌 Step 5: Implement JWT Authentication**  
Create an `ApplicationController` to handle JWT authentication.

Edit `app/controllers/application_controller.rb`:
```ruby
class ApplicationController < ActionController::API
  before_action :authorize_request

  def encode_token(payload)
    JWT.encode(payload, Rails.application.secret_key_base)
  end

  def authorize_request
    header = request.headers['Authorization']
    token = header.split(' ').last if header
    begin
      decoded = JWT.decode(token, Rails.application.secret_key_base)[0]
      @current_user = User.find(decoded['user_id'])
    rescue
      render json: { message: 'Unauthorized' }, status: :unauthorized
    end
  end
end
```

Create an `AuthenticationController`:
```bash
rails generate controller Authentication
```
Edit `app/controllers/authentication_controller.rb`:
```ruby
class AuthenticationController < ApplicationController
  skip_before_action :authorize_request, only: :login

  def login
    user = User.find_by(email: params[:email])
    if user&.authenticate(params[:password])
      token = encode_token(user_id: user.id)
      render json: { user: user, token: token }, status: :ok
    else
      render json: { message: 'Invalid credentials' }, status: :unauthorized
    end
  end
end
```

---

### **📌 Step 6: Test with Postman or cURL**
Start the server:
```bash
rails s
```

#### **1. Sign Up a New User**
```bash
curl -X POST http://localhost:3000/signup -H "Content-Type: application/json" -d '{"name":"John Doe", "email":"john@example.com", "password":"password", "role":"driver"}'
```

#### **2. Login to Get Token**
```bash
curl -X POST http://localhost:3000/login -H "Content-Type: application/json" -d '{"email":"john@example.com", "password":"password"}'
```

#### **3. Create a Vehicle**
```bash
curl -X POST http://localhost:3000/vehicles -H "Authorization: Bearer YOUR_JWT_TOKEN" -H "Content-Type: application/json" -d '{"plate_number":"XYZ123", "model":"Toyota Prius", "status":"available"}'
```

#### **4. Create a Trip**
```bash
curl -X POST http://localhost:3000/trips -H "Authorization: Bearer YOUR_JWT_TOKEN" -H "Content-Type: application/json" -d '{"origin":"Seattle", "destination":"Portland", "vehicle_id":1, "status":"pending"}'
```

---

### **🚀 Next Steps**
- ✅ **Deploy to AWS/GCP with Docker**
- ✅ **Add Real-Time Location Tracking (WebSockets)**
- ✅ **Integrate with a Mobile Frontend (React Native/Flutter)**
---
### **🚀 Frontend Integration for the TMS Mobile App using React Native**
Now that we have a **Ruby on Rails API** for the **Transportation Management System (TMS)**, we’ll build a **React Native frontend** to interact with it.

---

## **📌 Step 1: Initialize React Native Project**
First, make sure you have **Node.js** and **React Native CLI** installed.

### **1. Install React Native CLI (if not installed)**
It appears that nvm (Node Version Manager) isn't installed on your system. nvm lets you easily install and manage multiple versions of Node.js, and it also helps avoid global permission issues.

### To Install nvm:

1. **Download and Install via Script:**  
   Run the following command in your terminal:
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
   ```
   Alternatively, if you prefer wget:
   ```bash
   wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
   ```

2. **Restart Your Terminal:**  
   After the installation completes, close and reopen your terminal or source your profile:
   ```bash
   source ~/.bashrc
   ```
   (Replace `.bashrc` with `.zshrc` if you’re using zsh.)

3. **Verify Installation:**  
   Confirm nvm is installed by running:
   ```bash
   nvm --version
   ```

4. **Install Node.js Using nvm:**  
   Now you can install the latest Node.js version:
   ```bash
   nvm install node
   ```

Once nvm is set up and Node.js is installed, you should be able to install global npm packages without encountering permission errors.
---
This error indicates that you don't have permission to write to the global npm modules directory (`/usr/local/lib/node_modules`). Here are a few approaches to resolve this:

### 1. Use a Node Version Manager (Recommended)
Using a tool like [nvm](https://github.com/nvm-sh/nvm) allows you to manage Node versions and install global packages in your home directory, avoiding permission issues.

- Install nvm, then install a Node version with:
  ```bash
  nvm install node
  ```
- After that, you can run:
  ```bash
  //npm install -g react-native-cli
  npx @react-native-community/cli init TMSMobile
  ```
  without running into permission errors.

### 2. Change npm’s Default Directory for Global Packages
You can configure npm to use a directory in your home folder:

- Create a directory for global packages:
  ```bash
  mkdir ~/.npm-global
  ```
- Configure npm to use the new directory:
  ```bash
  npm config set prefix '~/.npm-global'
  ```
- Add the new directory to your PATH. For example, add this line to your `~/.profile` or `~/.bashrc`:
  ```bash
  export PATH=~/.npm-global/bin:$PATH
  ```
- Reload your profile:
  ```bash
  source ~/.profile
  ```


### 3. Use sudo (Less Recommended)
You can run the install command with elevated permissions, though this is generally not advised due to potential security risks and permission conflicts:
  
```bash
//sudo npm install -g react-native-cli
npx @react-native-community/cli init TMSMobile
```

Using one of these methods should help you resolve the EACCES permission error when installing global npm packages.```bash
npm install -g react-native-cli
```

### **2. Create a React Native Project**
```bash
npx react-native init TMSMobile
cd TMSMobile
```

### **3. Install Required Packages**
```bash
npm install axios react-navigation react-native-async-storage
```

---

## **📌 Step 2: Set Up API Integration**
We’ll create an **API service** to handle requests.

### **Create `api.js`**
Inside `TMSMobile/src/services/`, create a file `api.js`:
```javascript
import axios from 'axios';
import AsyncStorage from '@react-native-async-storage/async-storage';

const API_BASE_URL = "http://localhost:3000"; // Update for production

const api = axios.create({
  baseURL: API_BASE_URL,
  headers: { "Content-Type": "application/json" },
});

// Automatically attach JWT token for requests
api.interceptors.request.use(async (config) => {
  const token = await AsyncStorage.getItem("token");
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
}, error => Promise.reject(error));

export default api;
```

---

## **📌 Step 3: Implement User Authentication (Login & Signup)**
### **Create `AuthContext.js`**
Inside `TMSMobile/src/context/`, create `AuthContext.js`:
```javascript
import React, { createContext, useState, useEffect } from "react";
import AsyncStorage from "@react-native-async-storage/async-storage";
import api from "../services/api";

const AuthContext = createContext();

const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  useEffect(() => {
    loadUser();
  }, []);

  const loadUser = async () => {
    const token = await AsyncStorage.getItem("token");
    if (token) {
      api.get("/users/1") // Example: Fetch user details
        .then(response => setUser(response.data))
        .catch(() => logout());
    }
  };

  const login = async (email, password) => {
    try {
      const response = await api.post("/login", { email, password });
      await AsyncStorage.setItem("token", response.data.token);
      setUser(response.data.user);
    } catch (error) {
      console.error("Login failed", error);
    }
  };

  const logout = async () => {
    await AsyncStorage.removeItem("token");
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

export { AuthContext, AuthProvider };
```

### **Wrap the App in `AuthProvider`**
Modify `App.js`:
```javascript
import React from "react";
import { AuthProvider } from "./src/context/AuthContext";
import Navigation from "./src/navigation/Navigation";

const App = () => {
  return (
    <AuthProvider>
      <Navigation />
    </AuthProvider>
  );
};

export default App;
```

---

## **📌 Step 4: Create Login & Signup Screens**
### **Login Screen (`LoginScreen.js`)**
Inside `TMSMobile/src/screens/`, create `LoginScreen.js`:
```javascript
import React, { useState, useContext } from "react";
import { View, Text, TextInput, Button } from "react-native";
import { AuthContext } from "../context/AuthContext";

const LoginScreen = ({ navigation }) => {
  const { login } = useContext(AuthContext);
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  return (
    <View style={{ padding: 20 }}>
      <Text>Email:</Text>
      <TextInput style={{ borderWidth: 1, padding: 8, marginBottom: 10 }} value={email} onChangeText={setEmail} />
      <Text>Password:</Text>
      <TextInput style={{ borderWidth: 1, padding: 8, marginBottom: 10 }} secureTextEntry value={password} onChangeText={setPassword} />
      <Button title="Login" onPress={() => login(email, password)} />
      <Button title="Signup" onPress={() => navigation.navigate("Signup")} />
    </View>
  );
};

export default LoginScreen;
```

### **Signup Screen (`SignupScreen.js`)**
```javascript
import React, { useState } from "react";
import { View, Text, TextInput, Button } from "react-native";
import api from "../services/api";

const SignupScreen = ({ navigation }) => {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const signup = async () => {
    await api.post("/signup", { name, email, password, role: "driver" });
    navigation.navigate("Login");
  };

  return (
    <View style={{ padding: 20 }}>
      <Text>Name:</Text>
      <TextInput style={{ borderWidth: 1, padding: 8, marginBottom: 10 }} value={name} onChangeText={setName} />
      <Text>Email:</Text>
      <TextInput style={{ borderWidth: 1, padding: 8, marginBottom: 10 }} value={email} onChangeText={setEmail} />
      <Text>Password:</Text>
      <TextInput style={{ borderWidth: 1, padding: 8, marginBottom: 10 }} secureTextEntry value={password} onChangeText={setPassword} />
      <Button title="Signup" onPress={signup} />
    </View>
  );
};

export default SignupScreen;
```

---

## **📌 Step 5: Create Navigation**
Inside `TMSMobile/src/navigation/`, create `Navigation.js`:
```javascript
import React, { useContext } from "react";
import { createStackNavigator } from "@react-navigation/stack";
import { NavigationContainer } from "@react-navigation/native";
import { AuthContext } from "../context/AuthContext";
import LoginScreen from "../screens/LoginScreen";
import SignupScreen from "../screens/SignupScreen";
import DashboardScreen from "../screens/DashboardScreen";

const Stack = createStackNavigator();

const Navigation = () => {
  const { user } = useContext(AuthContext);

  return (
    <NavigationContainer>
      <Stack.Navigator>
        {user ? (
          <Stack.Screen name="Dashboard" component={DashboardScreen} />
        ) : (
          <>
            <Stack.Screen name="Login" component={LoginScreen} />
            <Stack.Screen name="Signup" component={SignupScreen} />
          </>
        )}
      </Stack.Navigator>
    </NavigationContainer>
  );
};

export default Navigation;
```

---

## **📌 Step 6: Running the App**
### **1. Start Rails API**
```bash
rails s
```

### **2. Start React Native App**
For Android:
```bash
npx react-native run-android
```
For iOS (Mac required):
```bash
npx react-native run-ios
```

---

## **🚀 Next Steps**
- ✅ **Vehicle List Screen**
- ✅ **Trip Management Screen**
- ✅ **Geolocation Tracking**
- ✅ **Push Notifications for Trip Updates**

---
### **🚀 Real-Time Tracking & Google Maps Integration in React Native TMS App**

Now, let's implement **real-time vehicle tracking** using **WebSockets** and **Google Maps** in our **React Native TMS mobile app**.

---

## **📌 Step 1: Add WebSockets to Rails API**
We'll use **Action Cable (Rails WebSockets)** for **real-time location updates**.

### **1. Configure Action Cable in Rails**
Edit `config/cable.yml`:
```yaml
development:
  adapter: redis
  url: redis://localhost:6379/1
```
Ensure Redis is installed:
```bash
brew install redis  # Mac
sudo apt install redis  # Linux
```
Start Redis:
```bash
redis-server
```

### **2. Create a WebSocket Channel for Tracking**
```bash
rails generate channel Tracking
```
Edit `app/channels/tracking_channel.rb`:
```ruby
class TrackingChannel < ApplicationCable::Channel
  def subscribed
    stream_from "tracking_#{params[:vehicle_id]}"
  end
end
```

### **3. Update Vehicle Controller to Broadcast Locations**
Edit `app/controllers/vehicles_controller.rb`:
```ruby
def update_location
  vehicle = Vehicle.find(params[:id])
  vehicle.update(latitude: params[:latitude], longitude: params[:longitude])

  ActionCable.server.broadcast "tracking_#{vehicle.id}", {
    latitude: vehicle.latitude,
    longitude: vehicle.longitude
  }

  render json: { message: "Location updated" }, status: :ok
end
```

### **4. Add a Route for Updating Locations**
Edit `config/routes.rb`:
```ruby
patch 'vehicles/:id/location', to: 'vehicles#update_location'
```

Start Rails with WebSockets:
```bash
rails s
```

---

## **📌 Step 2: Add WebSockets in React Native**
Install **WebSockets library**:
```bash
npm install @react-native-community/geolocation
```

### **Create `WebSocketService.js`**
Inside `TMSMobile/src/services/`, create `WebSocketService.js`:
```javascript
import { useEffect, useState } from "react";

const useWebSocket = (vehicleId) => {
  const [location, setLocation] = useState(null);

  useEffect(() => {
    const ws = new WebSocket(`ws://localhost:3000/cable`);
    
    ws.onopen = () => {
      ws.send(JSON.stringify({
        command: "subscribe",
        identifier: JSON.stringify({ channel: "TrackingChannel", vehicle_id: vehicleId })
      }));
    };

    ws.onmessage = (event) => {
      const response = JSON.parse(event.data);
      if (response.type === "ping" || !response.message) return;

      setLocation(response.message);
    };

    return () => ws.close();
  }, [vehicleId]);

  return location;
};

export default useWebSocket;
```

---

## **📌 Step 3: Implement Google Maps in React Native**
### **1. Install Dependencies**
```bash
npm install react-native-maps
npm install react-native-geolocation-service
```

### **2. Get Google Maps API Key**
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Enable **Google Maps SDK for Android & iOS**
3. Get API Key

Update `android/app/src/main/AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="YOUR_GOOGLE_MAPS_API_KEY"/>
```

Update `ios/TMSMobile/Info.plist`:
```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>App requires location access</string>
```

---

## **📌 Step 4: Create the Tracking Screen**
### **1. Create `TrackingScreen.js`**
Inside `TMSMobile/src/screens/`, create `TrackingScreen.js`:
```javascript
import React, { useEffect, useState } from "react";
import { View, Text } from "react-native";
import MapView, { Marker } from "react-native-maps";
import Geolocation from "@react-native-community/geolocation";
import api from "../services/api";
import useWebSocket from "../services/WebSocketService";

const TrackingScreen = ({ route }) => {
  const { vehicleId } = route.params;
  const location = useWebSocket(vehicleId);
  const [currentLocation, setCurrentLocation] = useState(null);

  useEffect(() => {
    Geolocation.getCurrentPosition(
      (position) => {
        const { latitude, longitude } = position.coords;
        setCurrentLocation({ latitude, longitude });
      },
      (error) => console.log(error),
      { enableHighAccuracy: true, timeout: 15000, maximumAge: 10000 }
    );
  }, []);

  useEffect(() => {
    if (location) {
      api.patch(`/vehicles/${vehicleId}/location`, location);
    }
  }, [location]);

  return (
    <View style={{ flex: 1 }}>
      {currentLocation && (
        <MapView
          style={{ flex: 1 }}
          initialRegion={{
            latitude: currentLocation.latitude,
            longitude: currentLocation.longitude,
            latitudeDelta: 0.01,
            longitudeDelta: 0.01,
          }}
        >
          {location && <Marker coordinate={location} title="Vehicle" />}
        </MapView>
      )}
    </View>
  );
};

export default TrackingScreen;
```

---

## **📌 Step 5: Add Tracking Navigation**
### **Modify `Navigation.js`**
Edit `TMSMobile/src/navigation/Navigation.js`:
```javascript
import TrackingScreen from "../screens/TrackingScreen";

<Stack.Screen name="Tracking" component={TrackingScreen} />
```

---

## **📌 Step 6: Running the App**
1. Start **Redis**:
   ```bash
   redis-server
   ```
2. Start **Rails Server**:
   ```bash
   rails s
   ```
3. Start **React Native App**:
   ```bash
   npx react-native run-android
   ```
   or for iOS:
   ```bash
   npx react-native run-ios
   ```

---

## **🚀 Next Steps**
✅ **Enhance UI with vehicle routes**  
✅ **Push Notifications for trip updates**  
✅ **Optimize WebSockets for performance**

---
### **🚀 Implementing Push Notifications for Trip Updates in React Native TMS App**
Now, let's integrate **push notifications** so that drivers and admins get real-time updates about trip status.

---

## **📌 Step 1: Set Up Firebase Cloud Messaging (FCM)**
We’ll use **Firebase Cloud Messaging (FCM)** to send push notifications.

### **1. Create a Firebase Project**
1. Go to [Firebase Console](https://console.firebase.google.com/).
2. Click **Create a Project**.
3. Enable **Cloud Messaging** under **Project Settings > Cloud Messaging**.
4. Download the **Google Services JSON (Android)** and **Google Services plist (iOS)**.

---

## **📌 Step 2: Install Firebase Packages**
In your React Native project, install Firebase dependencies:
```bash
npm install @react-native-firebase/app @react-native-firebase/messaging
```

For iOS:
```bash
cd ios && pod install && cd ..
```

---

## **📌 Step 3: Configure Firebase in React Native**
### **Android: Update `android/build.gradle`**
```gradle
dependencies {
    implementation platform('com.google.firebase:firebase-bom:32.2.2')
    implementation 'com.google.firebase:firebase-messaging'
}
```

### **iOS: Enable Push Notifications**
1. Enable **Push Notifications** in **Xcode > Signing & Capabilities**.
2. Enable **Background Modes > Remote Notifications**.

---

## **📌 Step 4: Implement Push Notifications in React Native**
### **1. Request Permission for Notifications**
Edit `TMSMobile/src/services/NotificationService.js`:
```javascript
import messaging from '@react-native-firebase/messaging';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { Alert } from 'react-native';

export async function requestUserPermission() {
  const authStatus = await messaging().requestPermission();
  const enabled = authStatus === messaging.AuthorizationStatus.AUTHORIZED || authStatus === messaging.AuthorizationStatus.PROVISIONAL;
  
  if (enabled) console.log('Notification permission granted.');
}

export async function getFCMToken() {
  let token = await AsyncStorage.getItem('fcmToken');
  if (!token) {
    try {
      token = await messaging().getToken();
      await AsyncStorage.setItem('fcmToken', token);
    } catch (error) {
      console.error("Failed to get FCM token:", error);
    }
  }
  return token;
}

export function notificationListener() {
  messaging().onNotificationOpenedApp(remoteMessage => {
    console.log('Notification caused app to open:', remoteMessage);
  });

  messaging().onMessage(async remoteMessage => {
    Alert.alert('New Trip Update', remoteMessage.notification.body);
  });

  messaging().setBackgroundMessageHandler(async remoteMessage => {
    console.log('Message handled in background:', remoteMessage);
  });
}
```

---

### **2. Call Notification Methods in `App.js`**
Edit `App.js`:
```javascript
import React, { useEffect } from 'react';
import { requestUserPermission, getFCMToken, notificationListener } from './src/services/NotificationService';

const App = () => {
  useEffect(() => {
    requestUserPermission();
    getFCMToken();
    notificationListener();
  }, []);

  return <Navigation />;
};

export default App;
```

---

## **📌 Step 5: Store FCM Token in Rails API**
### **1. Add FCM Token to Users Table**
Generate a migration:
```bash
rails generate migration AddFcmTokenToUsers fcm_token:string
rails db:migrate
```

### **2. Update `users_controller.rb`**
```ruby
class UsersController < ApplicationController
  def update_fcm_token
    @user = User.find(params[:id])
    if @user.update(fcm_token: params[:fcm_token])
      render json: { message: "FCM Token updated" }, status: :ok
    else
      render json: { errors: @user.errors.full_messages }, status: :unprocessable_entity
    end
  end
end
```

### **3. Add Route for Updating FCM Token**
Edit `config/routes.rb`:
```ruby
patch 'users/:id/fcm_token', to: 'users#update_fcm_token'
```

### **4. Send Token to API from React Native**
Edit `NotificationService.js`:
```javascript
import api from './api';

export async function sendTokenToServer(userId) {
  const token = await getFCMToken();
  if (token) {
    await api.patch(`/users/${userId}/fcm_token`, { fcm_token: token });
  }
}
```

---

## **📌 Step 6: Implement Notification Sending from Rails**
### **1. Install Firebase Admin SDK**
```bash
bundle add firebase_admin_sdk
```

### **2. Configure Firebase in Rails**
Edit `config/firebase_config.rb`:
```ruby
require 'firebase_admin_sdk'

FirebaseAdminSdk.configure do |config|
  config.project_id = "your-firebase-project-id"
  config.credentials = Rails.root.join("firebase.json").to_s
end
```

### **3. Create Notification Service**
Inside `app/services/notification_service.rb`:
```ruby
class NotificationService
  def self.send_push_notification(user, title, body)
    return unless user.fcm_token.present?

    FirebaseAdminSdk.messaging.send(
      {
        token: user.fcm_token,
        notification: {
          title: title,
          body: body
        }
      }
    )
  end
end
```

### **4. Send Notification on Trip Status Change**
Modify `app/controllers/trips_controller.rb`:
```ruby
def update_status
  trip = Trip.find(params[:id])
  trip.update(status: params[:status])

  NotificationService.send_push_notification(trip.user, "Trip Update", "Your trip is now #{trip.status}.")
  
  render json: { message: "Trip status updated" }, status: :ok
end
```

### **5. Add Route for Trip Status Update**
Edit `config/routes.rb`:
```ruby
patch 'trips/:id/status', to: 'trips#update_status'
```

---

## **📌 Step 7: Testing the Notifications**
### **1. Start Rails Server**
```bash
rails s
```

### **2. Update Trip Status via API**
```bash
curl -X PATCH http://localhost:3000/trips/1/status -H "Content-Type: application/json" -H "Authorization: Bearer YOUR_JWT_TOKEN" -d '{"status":"completed"}'
```

### **3. Observe Push Notifications on Mobile**
- A notification should appear when the **trip status** changes.
- Clicking the notification will open the app.

---

## **🚀 Next Steps**
✅ **Enhance UI for push notifications**  
✅ **Add notification preferences in the mobile app**  
✅ **Deploy Rails API & Firebase on cloud**

---
### **🚀 Deploying the Rails API to AWS/GCP & Improving UI with Notification Settings in React Native**

Now, let’s **deploy the Rails API** to a cloud provider (**AWS/GCP**) and enhance the **React Native UI** for notification settings.

---

# **📌 Step 1: Deploying Rails API on AWS (Elastic Beanstalk)**
We will deploy the Rails API using **AWS Elastic Beanstalk**, which simplifies deployment and scaling.

## **1. Install AWS Elastic Beanstalk CLI**
```bash
pip install awsebcli --upgrade --user
```

## **2. Initialize Elastic Beanstalk**
Inside your Rails project:
```bash
eb init
```
- Select a region (e.g., `us-west-2`).
- Choose **Ruby** as the platform.

## **3. Create and Deploy an Environment**
```bash
eb create tms-api-env
```
This will:
- **Launch an EC2 instance**
- **Set up PostgreSQL (AWS RDS)**
- **Auto-configure an S3 bucket for storage**

Once deployed, get the **Elastic Beanstalk URL**:
```bash
eb status
```
Example: `http://tms-api-env.us-west-2.elasticbeanstalk.com/`

## **4. Update API Base URL in React Native**
Edit `api.js`:
```javascript
const API_BASE_URL = "http://tms-api-env.us-west-2.elasticbeanstalk.com";
```

---

# **📌 Step 2: Deploying Rails API on GCP (Google Cloud Run)**
## **1. Install GCP CLI**
```bash
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
```

## **2. Build and Push Docker Image**
```bash
gcloud builds submit --tag gcr.io/YOUR_PROJECT_ID/tms-api
```

## **3. Deploy to Cloud Run**
```bash
gcloud run deploy tms-api --image gcr.io/YOUR_PROJECT_ID/tms-api --platform managed --allow-unauthenticated
```

Once deployed, update the **API Base URL** in React Native.

---

# **📌 Step 3: Enhancing React Native UI for Notification Settings**
Now, we’ll add a **notification settings page** where users can **toggle notifications**.

### **1. Create `NotificationSettingsScreen.js`**
Inside `TMSMobile/src/screens/`, create `NotificationSettingsScreen.js`:
```javascript
import React, { useState, useEffect } from "react";
import { View, Text, Switch } from "react-native";
import AsyncStorage from "@react-native-async-storage/async-storage";

const NotificationSettingsScreen = () => {
  const [notificationsEnabled, setNotificationsEnabled] = useState(true);

  useEffect(() => {
    (async () => {
      const storedValue = await AsyncStorage.getItem("notificationsEnabled");
      setNotificationsEnabled(storedValue === "true");
    })();
  }, []);

  const toggleSwitch = async () => {
    const newValue = !notificationsEnabled;
    setNotificationsEnabled(newValue);
    await AsyncStorage.setItem("notificationsEnabled", newValue.toString());
  };

  return (
    <View style={{ padding: 20 }}>
      <Text style={{ fontSize: 18, marginBottom: 10 }}>Push Notifications</Text>
      <Switch value={notificationsEnabled} onValueChange={toggleSwitch} />
    </View>
  );
};

export default NotificationSettingsScreen;
```

---

### **2. Update `NotificationService.js` to Respect User Preferences**
Modify `TMSMobile/src/services/NotificationService.js`:
```javascript
export function notificationListener() {
  messaging().onMessage(async remoteMessage => {
    const notificationsEnabled = await AsyncStorage.getItem("notificationsEnabled");
    if (notificationsEnabled === "true") {
      Alert.alert("New Trip Update", remoteMessage.notification.body);
    }
  });
}
```

---

### **3. Add Notification Settings to Navigation**
Modify `Navigation.js`:
```javascript
import NotificationSettingsScreen from "../screens/NotificationSettingsScreen";

<Stack.Screen name="Notification Settings" component={NotificationSettingsScreen} />
```

---

# **📌 Step 4: Testing & Deployment**
## **1. Restart Rails API**
```bash
rails s
```

## **2. Restart React Native App**
For Android:
```bash
npx react-native run-android
```
For iOS:
```bash
npx react-native run-ios
```

## **3. Test Push Notifications**
- Go to **Notification Settings** and **disable notifications**.
- Try **updating a trip status** via the API.
- Ensure **notifications don’t appear when disabled**.

---

# **🚀 Next Steps**
✅ **Add email/SMS notifications**  
✅ **Deploy Firebase backend on GCP/AWS**  
✅ **Optimize API for performance**

---
