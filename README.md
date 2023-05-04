# cmake-glad2
A drag'n'drop CMake project version of glad2 where all that is needed for it to be integrated into a pre-existing CMake project is to simply include the root directory as a subdirectory.

Credit must go to [Dav1dde](https://github.com/Dav1dde) (David Herberth) the original creator of [glad](https://github.com/Dav1dde/glad), [Robert Ryan](https://github.com/rtryan98) for making it CMake compatible, I simply added OpenGL ES 3.x support and upgraded the project to C++20.

Any questions or errors, please create an `Issue` and I will try my best to answer it as quickly as I can.

## Using:
```c++
#include <iostream>

#include <glad/gl.h>
#include <GLFW/glfw3.h>

// Window dimensions
const GLuint WIDTH = 800, HEIGHT = 600;

int main()
{
    glfwInit();

    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 6);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    glfwWindowHint(GLFW_RESIZABLE, GL_FALSE);

    GLFWwindow* window = glfwCreateWindow(WIDTH, HEIGHT, "CMake-GLAD2", NULL, NULL);
    glfwMakeContextCurrent(window);
    if (window == NULL)
    {
        std::cout << "Failed to create GLFW window" << std::endl;
        glfwTerminate();
        return -1;
    }

    // Load OpenGL functions, gladLoadGL returns the loaded version, 0 on error.
    int version = gladLoadGL(glfwGetProcAddress);
    if (version == 0)
    {
        std::cout << "Failed to initialize OpenGL context" << std::endl;
        return -1;
    }

    glViewport(0, 0, WIDTH, HEIGHT);

    // Game loop
    while (!glfwWindowShouldClose(window))
    {
        glfwPollEvents();

        // Render
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glfwSwapBuffers(window);
    }

    glfwTerminate();
    return 0;
}
```

## Building:

```cmake
# Simply add to any project as a subdirectory
add_subdirectory(engine/vendor/cmake-glad2)

# Rest of your CMakeLists.txt
...

# Include as a library
target_include_directories(${PROJECT_NAME} PUBLIC cmake-glad2)

# Linking statically / dynamically (you pick!)
if(WIN32)
    # Linking statically
    target_link_libraries(${PROJECT_NAME}
        -static
        cmake-glad2
        glfw
        glm
        assimp
        nfd
    )

    # Telling the compiler to not launch a console window when app is built
    target_link_options(${PROJECT_NAME} PRIVATE -mwindows)
else()
    # Linking dynamically
    target_link_libraries(${PROJECT_NAME}
        cmake-glad2
        glfw
        glm
        assimp
        nfd
    )
endif()
```