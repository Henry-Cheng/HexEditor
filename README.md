HexEditor
=========

Edit a hex file by address/offset/byte

Reference:
http://stackoverflow.com/questions/15798681/how-do-i-edit-an-executable-with-python-by-address-offset-bytes-like-in-a-hex-e
(For example: address = 0x00436411, and new hexadecimal = 0xFA)
You just need to open the executable as a file, for writing, in binary mode; then seek to the position you want to write; then write. So:

with open(path, 'r+b') as f:
    f.seek(position)
    f.write(new_bytes)
If you're going to be changing a lot of bytes, you may find it simpler to use mmap, which lets you treat the file as a giant list:

with open(path, 'r+b') as f:
    with contextlib.closing(mmap.mmap(f.fileno(), access=mmap.ACCESS_WRITE)) as m:
        m[first_position] = first_new_byte
        m[other_position] = other_new_byte
        # ...
If you're trying to write multi-byte values (e.g., a 32-bit int), you probably want to use the struct module.

If what you know is an address in memory at runtime, rather than a file position, you have to be able to map that to the right place in the executable file. That may not even be possible (e.g., a memory-mapped region). But if it is, you should be able to find out from the debugger where it's mapped. From inside a debugger, this is easy; from outside, you need to parse the PE header structures and do a lot of complicated logic, and there is no reason to do that.

I believe when using hex-ray IDA as a static disassembler, with all the default settings, the addresses it gives you are the addresses where the code and data segments will be mapped into memory if they aren't forced to relocate. Those are, obviously, not offsets into the file.
