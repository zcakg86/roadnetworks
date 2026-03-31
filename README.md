# roadnetworks
JS visualisation of speed limit data, using binary search

[![Speed Limit Visualization](https://github.com/user-attachments/assets/78b43896-61b0-49e3-a8c0-ba5f7e0248c4)](https://editor.p5js.org/zcakg86/full/sFe4gI1Xr)

```js
function setup() {
  createCanvas(800, 500);
  textAlign(CENTER, CENTER);
  textSize(14);

  const signs = [
    // speed, location
    new SpeedSign(40, 100),
    new SpeedSign(20, 150),
    new SpeedSign(40, 200),
    new SpeedSign(60, 350),
    new SpeedSign(70, 600),
    new SpeedSign(80, 850)
  ];

  // roadLength 1000, defaultSpeed 60
  speedMap = new SpeedLimitMap(1000, 60, signs);
}

function draw() {
  background(0,255,0);
  drawRoad();
  drawSigns();
  drawDefaultSpeed();
  drawMouseIndicator();
}

class SpeedSign {
  constructor(speed, signLocation) {
    this.speed = speed;
    this.signLocation = signLocation;
  }

  getSpeed() {
    return this.speed;
  }

  getSignLocation() {
    return this.signLocation;
  }
}

class SpeedLimitMap {
  constructor(roadLength, defaultSpeed, signs) {
    this.roadLength = roadLength;
    this.defaultSpeed = defaultSpeed;
    this.signs = [...signs];

    // Sort signs ascending by location for binary search 
    this.signs.sort((a, b) => a.getSignLocation() - b.getSignLocation());
  }

  getRoadLength() {
    return this.roadLength;
  }

  getDefaultSpeed() {
    return this.defaultSpeed;
  }

  getSigns() {
    return this.signs;
  }

  getSpeedAt(x) {
    // Reject locations outside the road
    if (x < 0 || x > this.roadLength) {
      throw new Error("Location is outside the road.");
    }

    // Inclusive search bounds
    let left = 0;
    let right = this.signs.length - 1;

    // Index of the last sign found whose location is <= x
    let resultIndex = -1;

    // Binary search
    while (left <= right) {
      // Middle index of the current search range
      const mid = Math.floor(left + (right - left) / 2);

      // Location of the sign at mid
      const signLocation = this.signs[mid].getSignLocation();

      // If this sign is at or before x, it is a valid candidate
      if (signLocation <= x) {
        // Save it as the best candidate found so far
        resultIndex = mid;

        // Search right half for a later valid sign
        left = mid + 1;
      } else {
        // This sign is after x, so search the left half
        right = mid - 1;
      }
    }

    // If no sign applies yet, use the default speed
    if (resultIndex === -1) {
      return this.defaultSpeed;
    }

    // Return the speed from the closest applicable sign
    return this.signs[resultIndex].getSpeed();
  }
}
//... rest at https://editor.p5js.org/zcakg86/full/sFe4gI1Xr
```
