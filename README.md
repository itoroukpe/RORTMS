# RORTMS
### **🚀 Sample Ruby on Rails Project: Transportation Management System (TMS) API for a Mobile App**  

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
```bash
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
