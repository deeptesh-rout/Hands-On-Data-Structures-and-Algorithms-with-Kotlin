#### 1. Modify HashMap to have a constructor which accepts a Map<K, V> object to create itself.

This constrcutor can be found in HashMap.kt file of Chapter04 section of this repository.

```
constructor(map: Map<K, V>) {
    val size = map.size
    val newSize = when {
        size < minCapacity -> minCapacity
        else -> fetchNearestCapacity(size)
    }
    this.table = arrayOfNulls(newSize)
    if (size > 0) {
        for (entry in map) {
            putVal(entry.key, entry.value)
        }
    }
}
```

#### 2. Implement a Map whose keys are primitive integers.

```
import java.util.*

class IntMap<V> constructor(capacity: Int = 10) {

    private var hashes: IntArray
    private var array: Array<Any?>

    var size = 0
        private set

    init {
        hashes = IntArray(if (capacity < 0) 0 else capacity)
        array = arrayOfNulls(if (capacity < 0) 0 else capacity)
    }

    fun isEmpty() = size <= 0

    operator fun get(key: Int) = get(key, null)

    fun get(key: Int, valueIfKeyNotFound: V?): V? {
        val index = Arrays.binarySearch(hashes, 0, size, key)
        return if (index < 0) valueIfKeyNotFound else array[index] as V
    }

    fun delete(key: Int) {
        val index = Arrays.binarySearch(hashes, 0, size, key)
        if (index >= 0) removeAt(index)
    }

    fun removeAt(index: Int) {
        System.arraycopy(hashes, index + 1, hashes, index, size - (index + 1))
        System.arraycopy(array, index + 1, array, index, size - (index + 1));
        size--
    }

    fun put(key: Int, value: V) {
        var index = Arrays.binarySearch(hashes, 0, size, key)
        if (index >= 0) array[index] = value
        else {
            index = index.inv()
            if (size >= hashes.size) {
                val newSize = if (size < 4) 4 else size + (size shr 1)
                val tempHashes = hashes
                val tempArray = array
                allocArrays(newSize)

                System.arraycopy(tempHashes, 0, hashes, 0, tempHashes.size)
                System.arraycopy(tempArray, 0, array, 0, tempArray.size)
            }

            if (index < size) {
                System.arraycopy(hashes, index, hashes, index + 1, size - index)
                System.arraycopy(array, index, array, index + 1, size - index)
            }

            hashes[index] = key
            array[index] = value
            size++
        }
    }

    fun indexOfKey(key: Int): Int {
        return Arrays.binarySearch(hashes, 0, size, key)
    }

    fun containsKey(key: Int) = indexOfKey(key) >= 0

    fun keyAt(index: Int) = hashes[index]

    fun valueAt(index: Int) = array[index] as V

    fun setValueAt(index: Int, value: V) {
        array[index] = value
    }

    private fun allocArrays(size: Int) {
        hashes = IntArray(size)
        array = arrayOfNulls(size)
    }
}
```
