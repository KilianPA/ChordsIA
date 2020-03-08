<template>
  <div>
    coucou
  </div>
</template>

<script>
import * as tf from '@tensorflow/tfjs'
// const tf = ''
import * as speechCommands from '@tensorflow-models/speech-commands'
export default {
  name: 'MainLayout',

  components: {
  },

  data () {
    return {
      model: '',
      recognizer: '',
      NUM_FRAMES: 3,
      INPUT_SHAPE: [this.NUM_FRAMES, 232, 1]
    }
  },
  mounted () {
    this.recognizer = speechCommands.create('BROWSER_FFT')
    this.recognizer.ensureModelLoaded()
    // this.listen()
    this.model = tf.loadLayersModel('file://./ressource/model/model.json')
  },
  methods: {
    recorder () {
      window.AudioContext = window.AudioContext || window.webkitAudioContext
      navigator.mediaDevices.getUserMedia({ audio: true }).then((stream) => {
      })
    },
    flatten (tensors) {
      const size = tensors[0].length
      const result = new Float32Array(tensors.length * size)
      tensors.forEach((arr, i) => result.set(arr, i * size))
      return result
    },
    listen () {
      if (this.recognizer.isListening()) {
        this.recognizer.stopListening()
        return
      }
      this.recognizer.listen(async ({ spectrogram: { frameSize, data } }) => {
        const vals = this.normalize(data.subarray(-frameSize * this.NUM_FRAMES))
        // const input = tf.tensor(vals, [1, ...this.INPUT_SHAPE])
        // const probs = this.model.predict(input)
        // const predLabel = probs.argMax(1)
        console.log(vals)
        // tf.dispose([input, probs, predLabel])
      }, {
        overlapFactor: 0.999,
        includeSpectrogram: true,
        invokeCallbackOnNoiseAndUnknown: true
      })
    },
    normalize (x) {
      const mean = -100
      const std = 10
      return x.map(x => (x - mean) / std)
    }
  }
}
</script>
