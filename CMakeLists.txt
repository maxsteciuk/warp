cmake_minimum_required(VERSION 3.15)
project(warp)

if(CMAKE_SYSTEM_NAME MATCHES FreeBSD)
    set(triple x86_64-unknown-freebsd)
elseif(CMAKE_SYSTEM_NAME MATCHES Linux)
    set(triple x86_64-unknown-linux-gnu)
else()
    set(triple x86_64-unknown-unknown)
endif()

set(BUILD_PACKER cargo build -p warp-packer --release --target ${triple})
set(BUILD_RUNNER cargo build -p warp-runner --release --target ${triple})

add_custom_target(warp ALL
                  COMMENT "Compiling warp-runner"
		  COMMAND CARGO_TARGET_DIR=${CMAKE_BINARY_DIR} ${BUILD_RUNNER} 
                  COMMAND strip ${CMAKE_BINARY_DIR}/${triple}/release/warp-runner
		  COMMAND CARGO_TARGET_DIR=${CMAKE_BINARY_DIR} ${BUILD_PACKER} 
                  COMMAND strip ${CMAKE_BINARY_DIR}/${triple}/release/warp-packer
)
