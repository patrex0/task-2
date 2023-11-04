import React from 'react';
import type {PropsWithChildren} from 'react';
import { useEffect, useRef, useState } from 'react';
import {
  ActivityIndicator,
  StyleSheet,
  useColorScheme,
  View,
  TouchableOpacity,
  Text,
} from 'react-native';

import { Camera, useCameraDevices, CameraProps } from 'react-native-vision-camera';
import { PermissionsAndroid } from 'react-native';
import { useCallback } from 'react';


function App(): JSX.Element {

  const devices = useCameraDevices()
  const device = devices.back
  const camera = useRef<Camera>(null);
  const [showCamera, setShowCamera] = useState(false);
  const [imageSource, setImageSource] = useState('');


  useEffect(() => {
    (async () => {
    //check camera permessions:
    const newCameraPermission = await Camera.requestCameraPermission();
    const newMicrophonePermission = await Camera.requestMicrophonePermission();

    try {
      const granted = await PermissionsAndroid.request(
        PermissionsAndroid.PERMISSIONS.WRITE_EXTERNAL_STORAGE,
        {
          title: "Cool Photo App Camera Permission",
          message:
            "Your app needs permission.",
          buttonNeutral: "Ask Me Later",
          buttonNegative: "Cancel",
          buttonPositive: "OK"
        }
      );
      if (granted === PermissionsAndroid.RESULTS.GRANTED) {
        return true;
      } else {
        console.log("Camera permission denied");
        return false;
      }
    } catch (err) {
      console.warn(err);
      return false;
    }
  })();
  }, []);

  const capturePhoto = async () => {
    if (camera.current !== null) {
      const photo = await camera.current.takePhoto({});
      setImageSource(photo.path);
      setShowCamera(false);
      console.log(photo.path);
    }
  };

  if (device == null) return <ActivityIndicator />

  return (

      <View style={styles.camera}>
        <Camera
        ref={camera}
          style={StyleSheet.absoluteFill}
          device={device}
          isActive={true}
          photo={true}
          zoom={1}
        />
        <TouchableOpacity onPress={capturePhoto} style={styles.captureButton}>
          <Text style={{ fontSize: 18, marginBottom: 10, color: 'white'  }}>Capture Photo</Text>
        </TouchableOpacity>
         </View>
         

  );
}

const styles = StyleSheet.create({
  sectionContainer: {
    marginTop: 32,
    paddingHorizontal: 24,
    flex:0.3,

  },
  all: {
    flex: 1,
  },
  camera: {
    flex: 1,
    alignItems: 'center',

  },
  captureButton: {
    backgroundColor: 'blue',
    padding: 10,
    borderRadius: 5,
  },
  

});

export default App;
