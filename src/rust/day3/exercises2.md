```rust

mod ffi {
    use std::os::raw::{c_char, c_int, c_long, c_ulong, c_ushort};

    // Opaque type. See https://doc.rust-lang.org/nomicon/ffi.html.
    #[repr(C)]
    pub struct DIR {
        _data: [u8; 0],
        _marker: core::marker::PhantomData<(*mut u8, core::marker::PhantomPinned)>,
    }

    // Layout as per readdir(3) and definitions in /usr/include/x86_64-linux-gnu.
    #[repr(C)]
    pub struct dirent {
        pub d_ino: c_long,
        pub d_off: c_ulong,
        pub d_reclen: c_ushort,
        pub d_type: c_char,
        pub d_name: [c_char; 256],
    }

    extern "C" {
        pub fn opendir(s: *const c_char) -> *mut DIR;
        pub fn readdir(s: *mut DIR) -> *const dirent;
        pub fn closedir(s: *mut DIR) -> c_int;
    }
}

use std::ffi::{CStr, CString, OsStr, OsString};
use std::os::unix::ffi::OsStrExt;
use crate::ffi::opendir;

#[derive(Debug)]
struct DirectoryIterator {
    path: CString,
    dir: *mut ffi::DIR,
}

impl DirectoryIterator {
    fn new(path: &str) -> Result<DirectoryIterator, String> {
        unsafe {
            let path = CString::new(path).map_err(|err| format!("invalid path {err}"))?;
            let dir = ffi::opendir(path.as_ptr());
            if dir.is_null() {
                return Err(format!("cannot not open {:?}", path))
            }
            Ok( DirectoryIterator{path, dir})
        }
    }
}

impl Iterator for DirectoryIterator {
    type Item = OsString;

    fn next(&mut self) -> Option<OsString> {
        let dirent = unsafe {ffi::readdir(self.dir)};
        if dirent.is_null() {
            return None;
        }
        let d_name = unsafe {CStr::from_ptr((*dirent).d_name.as_ptr())};
        let os_str = OsStr::from_bytes(d_name.to_bytes());

        Some(os_str.to_owned())
    }
}

fn main() -> Result<(), String> {
    let iter = DirectoryIterator::new(".")?;
    println!("files: {:#?}", iter.collect::<Vec<_>>());
    Ok(())
}
```

Solution is under [here](https://github.com/google/comprehensive-rust/blob/main/src/exercises/day-3/safe-ffi-wrapper.rs)