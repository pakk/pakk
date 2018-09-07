# pakk

Encrypt and pack several files or folders into a single invokable file.

## CLI

## Package

pakk provides a [few functions and types](#API) for working with pakk buffers and files. In most use cases, `pakk_files` and `unpakk` are used to create pakk files and access data from pakk files.

### Example

Keys should be AES-compatible and are typically bytes-like objects:
```py
    import hashlib

    password = "kitty"
    key = hashlib.sha256(password.encode("utf-8")).digest()
```

You can pakk a single file using `pakk_files`:
```py
    with open("./outputfile.pakk", "wb") as out_file:
        pakk_files(key, ["./some/file"], out_file)
```

And to unpakk back into files, use `unpakk_files`:
```py
    unpakk_files(key, "./outputfile.pakk")
```

If you want to invoke a pakks file without unpakking it into the original file structure, use `unpakk`:
```py
    with open("./outputfile.pakk", "rb") as in_file:
        pakk = unpakk(key, in_file)

    for key, blob in pakk.blobs.items():
        print(f"{key}: {blob.name}")
```

### API

```py
class PakkBlob:
    """A named stream of blob data, typically represents a single pakk file.

    Attributes:
        name (str): The preferrably unique but pronouncable name of the blob.
        size (int): The size of the blob in bytes.
        stream (:obj:`io.BufferedIOBase`): A buffered stream to the blob of data.
    """

class Pakk:
    """A collection of blobs. Typically correlates to a pakk file.

    Attributes:
        blobs (:obj:`dict` of :obj:`str` to :obj:`PakkBlob`): a dictionary of blobs in this pakk where the key is each blob's name.
    """

def pakk(key: bytes, data: List[PakkBlob], output: io.BufferedIOBase, chunksize=64*1024):
    """Encrypt and pakk an input :class:`list` of :class:`PakkBlob` into a single buffered output.

    .. note::

        Appends to the current position in the output stream.

    Args:
        key (bytes): the key to encrypt the incoming blobs with, using SHA256
        data (list of :class:`PakkBlob`): the blobs to encrypt and pakk into the output buffer
        output (:class:`io.BufferedIOBase`): the buffer to write the pakk file data to

    Kwargs:
        chunksize (int): the max size, in bytes, of the chunks to write to the output buffer
    """

def unpakk(key: bytes, data: io.BufferedIOBase, chunksize=24*1024) -> Pakk:
    """Unpakk and decrypt a buffered stream of pakk file data.

    .. note::

        Reads from the current position in the stream.

    Args:
        key (bytes): the key to decrypt the pakk blobs with, using SHA256
        data (:class:`io.BufferedIOBase`): the buffer to read pakk file data from

    Kwargs:
        chunksize (int): the max size, in bytes, of the chunks to read from the data buffer

    Returns:
        :class:`Pakk`. The pakks blob info and decrypted blob data.
    """

def pakk_bytes(key, data: List[bytes], output: io.BufferedIOBase):
    """Encrypt and pakk an input :class:`list` of :class:`bytes` into a single buffered output.

    .. note::

        Appends to the current position in the output stream.

    Args:
        key (bytes): the key to encrypt the incoming blobs with, using SHA256
        data (list of :class:`bytes`): the bytes objects to encrypt and pakk into the output buffer
        output (:class:`io.BufferedIOBase`): the buffer to write the pakk file data to

    Kwargs:
        chunksize (int): the max size, in bytes, of the chunks to write to the output buffer
    """

def unpakk_bytes(key, data: io.BufferedIOBase) -> List[bytes]:
    """Unpakk and decrypt a buffered stream of pakk file data into a list of bytes objects.

    .. note::

        Reads from the current position in the stream.

    Args:
        key (bytes): the key to decrypt the pakk blobs with, using SHA256
        data (:class:`io.BufferedIOBase`): the buffer to read pakk file data from

    Kwargs:
        chunksize (int): the max size, in bytes, of the chunks to read from the data buffer

    Returns:
        list of bytes-objects. The decrypted blob data from the pakk file stream.
    """

def pakk_files(key, files: List[str], destination: Union[io.BufferedIOBase, str], chunksize=64*1024):
    """Encrypt and pakk specified files and folders into a single buffered output.

    .. note::

        Appends to the current position in the output stream.

    Args:
        key (bytes): the key to encrypt the incoming blobs with, using SHA256
        files (list of strings): the list of file and folders to encrypt and pakk
        destination (:class:`io.BufferedIOBase` or str): the buffer or file path to write the pakk file data to

    Kwargs:
        chunksize (int): the max size, in bytes, of the chunks to write to the output buffer
    """

def unpakk_files(key, file: str, destination=os.curdir, chunksize=24*1024):
    """Unpakk and decrypt a pakk file at a specified path, writing the data into the original file structure at a specified root path.

    Args:
        key (bytes): the key to decrypt the pakk blobs with, using SHA256
        file (str): the path of the pakk file to unpakk and decrypt
        destination (str): the folder to output the unpakked files to

    Kwargs:
        chunksize (int): the max size, in bytes, of the chunks to read from the data buffer
    """
```
