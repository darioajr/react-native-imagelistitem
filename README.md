# react-native-image-listitem [![Travis](https://img.shields.io/travis/oblador/react-native-image-listitem.svg)](https://travis-ci.org/oblador/react-native-image-listitem) [![npm version](https://img.shields.io/npm/v/react-native-image-listitem.svg)](https://www.npmjs.com/package/react-native-image-listitem)
*ListItem gallery for images in React Native*

## Installation

```
yarn add react-native-image-listitem
```

**Note: Progress indicators has been broken out to a separate component; [react-native-progress](https://github.com/oblador/react-native-progress). To use them, please follow installation instructions for that package.**


## Usage

```js
import Image from 'react-native-image-listitem';

<ImageListItem
  id={index}
  onPressItem={this.onPressItem}
  uri={item.image.uri}
  selected={item.selected}
/>
```

## Properties

Any [`Image` property](http://facebook.github.io/react-native/docs/image.html) and the following:

| Prop | Description | Default |
|---|---|---|
|**`styles`**|Styles applied to the inner image component.|*None*|
|**`id`**|id of the component.|*None*|
|**`uri`**|uri of the component.|*None*|
|**`selected`**|Enumerate selected images|*None*|

## Demo

![image-progress-demo](https://cloud.githubusercontent.com/assets/378279/10882718/0f33e7b4-813b-11e5-9f6c-90df8b9050b8.gif)

## Example 

Check full example in the `Example` folder. 

### Pie

```js
import React from 'react'
import { StyleSheet, View, Image, PixelRatio, FlatList, Text, TouchableOpacity, AsyncStorage, Dimensions } from 'react-native'
import { Button, ListItem } from 'react-native-elements'

import ImagePicker from 'react-native-image-picker'
import ImageListItem from 'react-native-image-listitem'

const options = {
  title: 'Select Avatar',
  storageOptions: {
    skipBackup: true,
    path: 'images'
  }
}

class MainScreen extends React.Component {
  
  constructor (props) {
    super(props);
    this.state={
      selectedImage: null,
      dataSource: []
    }
    
    this.selectPhotoTapped = this.selectPhotoTapped.bind(this)
    this.renderRow = this.renderRow.bind(this)
    this.onPressItem = this.onPressItem.bind(this)
  }

  componentWillMount() {
    try {
      AsyncStorage.getItem("ImageArray", (err, result) => {
        if (err === null && result !== null) {
          dataSource = JSON.parse(result)       
          this.setState({
            dataSource: dataSource
          })
        }
      })
    } catch (err) {
      console.warn(err)
    }
  }

  selectPhotoTapped() {
    ImagePicker.showImagePicker(options, (response) => {

      if (response.error) {
        console.warn('ImagePicker Error: ', response.error)
      } else {
        let source = {
          image: {uri: response.uri},
          selected: false
        }

        // You can also display the image using data:
        // let source = { uri: 'data:image/jpeg;base64,' + response.data };
        if (this.state.selectedImage === null) {
          this.state.selectedImage = source.image
        }

        this.state.dataSource.push(source)

        this.setState({
          selectedIndex:this.state.selectedIndex
        })

        try {
          AsyncStorage.setItem("ImageArray", JSON.stringify(this.state.dataSource))
        } catch(err) {
          console.warn(err)
        }
      }
    })
  }

  deleteSelectedImage() {
    newDataSource = [];
    this.state.dataSource.forEach(element => {
      if (!element.selected) {
        newDataSource.push(element)
      }
    })

    this.setState({
      dataSource: newDataSource
    })

    try {
      AsyncStorage.setItem("ImageArray", JSON.stringify(newDataSource))
    } catch(err) {
      console.warn(err)
    }
  }

  uploadImages() {
    console.warn('not implemented.')
  }

  selectPhotoTapped() {
    ImagePicker.showImagePicker(options, (response) => {

      if (response.error) {
        console.warn('ImagePicker Error: ', response.error)
      } else {
        let source = {
          image: {uri: response.uri},
          selected: false
        }

        // You can also display the image using data:
        // let source = { uri: 'data:image/jpeg;base64,' + response.data };
        if (this.state.selectedImage === null) {
          this.state.selectedImage = source.image
        }

        this.state.dataSource.push(source)

        this.setState({
          selectedIndex:this.state.selectedIndex
        })

        try {
          AsyncStorage.setItem("ImageArray", JSON.stringify(this.state.dataSource))
        } catch(err) {
          console.warn(err);
        }
      }
    })
  }

  onPressItem = (index) => {
    this.state.dataSource[index].selected = !this.state.dataSource[index].selected;
    
    this.setState({
      dataSource:this.state.dataSource,
      selectedImage:this.state.dataSource[index].image
    })
  }

  renderRow = ({item, index}) => (
    <ImageListItem
      id={index}
      onPressItem={this.onPressItem}
      uri={item.image.uri}
      selected={item.selected}
    />
  )

  keyExtractor = (item, index) => `${index}`

  render() {
    return (
      <View style={styles.container}>
        <View style={styles.topbar}>
          <Button title='Upload' onPress={this.uploadImages.bind(this)} />
          <Button title='Delete' onPress={this.deleteSelectedImage.bind(this)} />
        </View>
        <View style={styles.content}>
          <Image style={styles.image} source={this.state.selectedImage} />
        </View>
        <View style={styles.bottombar}>
          <FlatList
            style={styles.imageArray}
            horizontal={true}
            keyExtractor={this.keyExtractor}
            data={this.state.dataSource}
            extraData={this.state}
            renderItem={this.renderRow}
          />
          
          <TouchableOpacity
            style={styles.newButton}
            onPress={this.selectPhotoTapped.bind(this)}>
            <Text>New</Text>
          </TouchableOpacity>
        </View>
      </View>
    )
  }
}

MainScreen.navigationOptions = {
  header: null
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F5FCFF',
    flexDirection: 'column',
    justifyContent: 'space-between',
    alignItems: 'center'
  },
  topbar: {
    flexDirection: 'row'
  },
  content: {
    flex: 1,
    width: '100%',
    alignSelf: 'stretch',
  },
  bottombar: {
    width: '100%',
    height: 80,
  },
  image: {
    width: '100%',
    height: '100%'
  },
  imageArray: {
    position: 'absolute',
    marginLeft: 80,
    height: 80,
    marginRight: 0
  },
  newButton: {
    width: 80,
    height: 80,
    alignItems: 'center',
    justifyContent: 'center',
    borderColor: 'grey',
    backgroundColor: 'white',
    borderWidth: 1
  }
})

export default MainScreen
```

## License

[MIT License](http://opensource.org/licenses/mit-license.html). © Dario Alves Junior

