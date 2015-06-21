��������JoeyDeVries����[Django](http://bullteacher.com/20-mesh.html)������[http://learnopengl.com](http://learnopengl.com/)

ʹ��Assimp���԰Ѷ��ֲ�ͬ��ʽ��ģ�ͼ��ص����������һ�����룬���ǾͶ�������ΪAssimp�Լ������ݽṹ���������յ��뷨�ǰ���Щ����ת��ΪOpenGL�ɶ������ݣ�������Ⱦ���塣���Ǵ�ǰ��Ľ̳��˽⵽��һ���������һ���ɻ���ʵ�壬�������ǾͶ���һ���Լ��������ࡣ

������ϰһ��Ŀǰѧ��֪ʶ������һ������������Ҫ��Щ���ݡ�һ������Ӧ��������Ҫһ�鶥�㣬ÿ���������һ��λ��������һ������������һ����������������һ������ҲӦ�ð���һ�����������õ�������������diffuse/specular map����ʽ���ֵĲ������ݡ�

Ϊ����OpenGL�ж���һ�����㣬����������������������һ��������:


```c++
struct Vertex
{
    glm::vec3 Position;
    glm::vec3 Normal;
    glm::vec2 TexCoords;
};
```
���ǰ�ÿ����Ҫ���������浽һ������Vertex�Ľṹ���У�������������ÿ���������ԡ��������Vertex�ṹ���⣬����Ҳϣ����֯�������ݣ��������Ƕ���һ��Texture�ṹ�壺


```c++
struct Texture
{
    GLuint id;
    String type;
};
```
���Ǵ��������id���������ͣ�����diffuse�������specular����

֪���˶���������ʵ�ʱ����ǿ��Կ�ʼ����������Ľṹ��


```c++
class Mesh
{
Public:
    vector<Vertex> vertices;
    vector<GLuint> indices;
    vector<Texture> textures;
    Mesh(vector<Vertex> vertices, vector<GLuint> indices, vector<Texture> texture);
    Void Draw(Shader shader);
 
private:
    GLuint VAO, VBO, EBO;
    void setupMesh();
}
```
�������������һ�㶼�����ӣ����췽�������ǳ�ʼ���������б������ݡ���setupMesh�������ʼ�����塣���ͨ��Draw������������ע�⣬���ǰ�shader���ݸ�Draw������ͨ����shader���ݸ�mesh���ڻ���֮ǰ�������ü���uniform���������Ӳ�����������Ԫ����

���캯�������ݷǳ�ֱ�ӡ����Ǽ�������Ĺ��б�����ʹ�õ��ǹ��캯����Ӧ�Ĳ����������ڹ��캯����Ҳ����setupMesh������


```c++
Mesh(vector<Vertex> vertices, vector<GLuint> indices, vector<Texture> textures)
{
    this->vertices = vertices;
    this->indices = indices;
    this->textures = textures;
 
    this->setupMesh();
}
```
����ûʲô�ر�ģ������������о�һ��setupMesh������

 

##��ʼ��

����������һ���е��������ݿ�������Ⱦ����Ҫ��л���캯��������ȷʵ��Ҫ���ú��ʵĻ��壬ͨ����������ָ�루vertex attribute pointers�����嶥����ɫ��layout��������Ӧ�ö���Щ�������Ϥ��������������ͨ�������˽ṹ����ʹ�ö������ݣ�������΢�е㲻һ����


```c++
void setupMesh()
{
    glGenVertexArrays(1, &this->VAO);
    glGenBuffers(1, &this->VBO);
    glGenBuffers(1, &this->EBO);
  
    glBindVertexArray(this->VAO);
    glBindBuffer(GL_ARRAY_BUFFER, this->VBO);
 
    glBufferData(GL_ARRAY_BUFFER, this->vertices.size() * sizeof(Vertex), 
                 &this->vertices[0], GL_STATIC_DRAW);  
 
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, this->EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, this->indices.size() * sizeof(GLuint), 
                 &this->indices[0], GL_STATIC_DRAW);
 
    // Vertex Positions
    glEnableVertexAttribArray(0); 
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), 
                         (GLvoid*)0);
    // Vertex Normals
    glEnableVertexAttribArray(1); 
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), 
                         (GLvoid*)offsetof(Vertex, Normal));
    // Vertex Texture Coords
    glEnableVertexAttribArray(2); 
    glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, sizeof(Vertex), 
                         (GLvoid*)offsetof(Vertex, TexCoords));
 
    glBindVertexArray(0);
}
```
�����������ûʲô�ر�ͬ�ĵط�����Vertex�ṹ��İ���������һЩС��Ϸ��

C++�Ľṹ����һ����Ҫ�����ԣ��Ǿ������ڴ��������������ġ���������ýṹ���ʾһ�����ݣ�����ṹ��ֻ�����ṹ��������ı��������ͻ�ֱ��ת��Ϊһ��float��ʵ������byte�����飬���Ǿ�������һ�����黺�壨array buffer�����ˡ����磬����������һ��Vertex�ṹ�壬�����ڴ��е��Ų����ڣ�


```c++
Vertex vertex;
vertex.Position = glm::vec3(0.2f, 0.4f, 0.6f);
vertex.Normal = glm::vec3(0.0f, 1.0f, 0.0f);
vertex.TexCoords = glm::vec2(1.0f, 0.0f);
// = [0.2f, 0.4f, 0.6f, 0.0f, 1.0f, 0.0f, 1.0f, 0.0f];
```
��л������õ����ԣ�������ֱ�Ӱ�һ����Ϊ�������ݵ�һ����Vertex�ṹ���ָ�봫�ݹ�ȥ�����ǻᷭ���glBufferData���õĲ�����


```c++
glBufferData(GL_ARRAY_BUFFER, this->vertices.size() * sizeof(Vertex), 
             &this->vertices[0], GL_STATIC_DRAW);
```
��Ȼ�أ�sizeof����Ҳ����ʹ���ڽṹ���������ֽ����͵Ĵ�С����Ӧ����32�ֽڣ�8float * 4����

һ��Ԥ����ָ�����offsetof(s, m)�ѽṹ����Ϊ���ĵ�һ���������ڶ�������������ṹ�����ֵı��������ǽṹ�������һ����Ҫ��;������������������ӽṹ�忪ʼ���ֽ�ƫ������offset��������ڶ���glVertexAttribPointer����ƫ��������Ч���ܺã�


```c++
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), 
                     (GLvoid*)offsetof(Vertex, Normal));
```
ƫ��������ʹ��offsetof���������ˣ��������������÷����������ֽ�ƫ�������ڷ��������ڽṹ����ֽ�ƫ����������3float��Ҳ����12�ֽڣ�һ��floatռ4�ֽڣ���ע�⣬����ͬ�����ò�����������Vertex�ṹ��Ĵ�С��

ʹ��һ���������Ľṹ�壬�������ṩ�ɶ��Ը��ߵĴ���ͬʱҲ�����ǿ������ɵ���չ�ṹ�塣���������Ҫ������һ���������ԣ����ǰ������Լ򵥵���ӵ��ṹ���У��������Ŀ���չ�ԣ���Ⱦ���벻�ᱻ�ƻ���

 

##��Ⱦ

������ҪΪMesh�ඨ������һ��������������Draw��������������Ⱦǰ����ϣ���󶨺��ʵ�����Ȼ�����glDrawElements������Ϊ���Ǵ�һ��ʼ��֪����������ж��������Լ�����Ӧ����ʲô���͵ģ���������±�ú����ѡ��������Ǹ���������ɫ������������Ԫ�Ͳ������أ�

���������⣬������Ҫ����һ���ض������ƹ�����ÿ��diffuse��������Ϊtexture_diffuseN,ÿ��specular����Ӧ�ñ�����Ϊtexture_specularN.N��һ����1���������������ʹ�õ����ֵ֮�����������˵����һ��������������3��diffuse�����2��specular�������ǵ����������Ӧ�����������ã�


```c++
uniform sampler2D texture_diffuse1;
uniform sampler2D texture_diffuse2;
uniform sampler2D texture_diffuse3;
uniform sampler2D texture_specular1;
uniform sampler2D texture_specular2;
```
ʹ�������Ĺ����������ܶ�����������ɫ������Ҫ����������������������һ����������У���ô�ࣩ�������Ǿ�֪�����ǵ�����Ӧ����ʲô���������Ҳʹ�����ܹ�����һ�������ϵ��κ�����������ͨ��������ʵĲ����������߿�������ʹ��ϣ��ʹ�õ���������Ȼ�����ٵĻ��ͻ��е��˷Ѱ󶨺�uniform�����ˣ���

�������������кܶ಻ͬ�Ľ������������㲻ϲ�����������������Լ�����һ�����Լ��ķ�����
���Ļ��ƴ��룺


```c++
void Draw(Shader shader) 
{
    GLuint diffuseNr = 1;
    GLuint specularNr = 1;
    for(GLuint i = 0; i < this->textures.size(); i++)
    {
        glActiveTexture(GL_TEXTURE0 + i); // Activate proper texture unit before binding
        // Retrieve texture number (the N in diffuse_textureN)
        stringstream ss;
        string number;
        string name = this->textures[i].type;
        if(name == "texture_diffuse")
            ss << diffuseNr++; // Transfer GLuint to stream
        else if(name == "texture_specular")
            ss << specularNr++; // Transfer GLuint to stream
        number = ss.str(); 
 
        glUniform1f(glGetUniformLocation(shader.Program, ("material." + name + number).c_str()), i);
        glBindTexture(GL_TEXTURE_2D, this->textures[i].id);
    }
    glActiveTexture(GL_TEXTURE0);
 
    // Draw mesh
    glBindVertexArray(this->VAO);
    glDrawElements(GL_TRIANGLES, this->indices.size(), GL_UNSIGNED_INT, 0);
    glBindVertexArray(0);
}
```
�ⲻ����Ư���Ĵ��룬��������Ҫ�����C++ת������ʱ�ĳ�ª������intתstringʱ���������ȼ���N-Ԫ��ÿ���������ͣ��������ӵ����������ַ�������ȡ���ʵ�uniform����Ȼ����Һ��ʵĲ�����λ�ã�����λ��ֵ��Ӧ��ǰ��������Ԫ����������Ҳ��������Ҫ��Draw��������shader��ԭ��������ӡ�material.������Ϊ�����uniform������Ϊ����ͨ��������������ʽṹ�壨����ÿ��ʵ��Ҳ����в�ͬ����

ע�⣬�����ǰ�diffuse��specular���ݵ��ַ�������stringstream����ʱ�򣬼����������ӣ���C++��������������++�������ȷ�������Ȼ���1����++�������ȼ�1�ٷ����������ǵ�����������ȴ���ԭ���ļ�����ֵ���ַ�������Ȼ���ټ�1����һ����Ч��
����Դ�����õ�Mesh���Դ�롣

Mesh���Ƕ�����ǰ��Ľ̳������۵ĺܶ໰��ĵļ��ĳ���������Ľ̳�����ǻᴴ��һ��ģ�ͣ��������˷Ŷ�����������������������ʵ��Assimp�ļ��ؽӿڡ�