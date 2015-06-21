��������JoeyDeVries����[Django](http://bullteacher.com/)������[http://learnopengl.com](http://learnopengl.com/)

������ʱ����������Assimp������ʼ����ʵ�ʵļ��غ�ת�������ˡ����̵̳�Ŀ���Ǵ�����һ���࣬�������Ա��ģ�͵�ȫ������ȷ�е�˵��һ��ģ�Ͱ����������һ��������ܴ��ж������һ������������һ��ľ����̨��һ���ⶥ����Ҳ��һ����Ӿ�أ�����Ȼ������Ϊһ����һģ�͡�����ͨ��Assimp����ģ�ͣ�������ת��Ϊ�������Mesh��������Щ����������ǰ�̳��ﴴ���ġ�

�л���˵���Ұ�Model��Ľṹ���ָ��㣺


```c++
class Model 
{
    public:
        /*  Functions   */
        Model(GLchar* path)
        {
            this->loadModel(path);
        }
        void Draw(Shader shader); 
    private:
        /*  Model Data  */
        vector<Mesh> meshes;
        string directory;
        /*  Functions   */
        void loadModel(string path);
        void processNode(aiNode* node, const aiScene* scene);
        Mesh processMesh(aiMesh* mesh, const aiScene* scene);
        vector<Texture> loadMaterialTextures(aiMaterial* mat, aiTextureType type, string typeName);
};
```
Model�����һ��Mesh�����������������Ҫ�ڹ��캯���и����ļ���λ�á�֮���ڹ������У���ͨ��loadModel���������ļ���˽�з����������Ϊ����һ���ֵ�Assimp����ĳ��涯�������ǻ�򵥽������ǡ�ͬ�������Ǵ����ļ�·����Ŀ¼�������Ժ���������ʱ����õ���

����Drawû��ʲô�ر�֮������������ѭ��ÿ�����񣬵��ø��Ե�Draw������


```c++
void Draw(Shader shader)
{
    for(GLuint i = 0; i < this->meshes.size(); i++)
        this->meshes[i].Draw(shader);
}
```

##��һ��3Dģ�͵��뵽OpenGL

Ϊ�˵���һ��ģ�ͣ�������ת��Ϊ�����Լ������ݽṹ����һ����Ҫ�������ǰ������ʵ�Assimpͷ�ļ��������������Ͳ�������Ǳ�Թ�ˡ�


```c++
#include <assimp/Importer.hpp>
#include <assimp/scene.h>
#include <assimp/postprocess.h>
```
���ǽ�Ҫ���õĵ�һ��������loadModel,�������캯��ֱ�ӵ��á���loadModel�������棬����ʹ��Assimp����ģ�͵�Assimp�б���Ϊscene��������ݽṹ������ܻ��ǵ�ģ�ͼ���ϵ�еĵ�һ���̳��У�����Assimp�����ݽṹ�ĸ�����һ���������˳����������Ǿ��ܴ��Ѽ���ģ���л�ȡ�������������ˡ�

Assimp����ŵ��ǣ�����Լ�ĳ��������������в�ͬ��ʽ�ļ��ļ���ϸ�ڣ���һ�п���������һ�У�


```c++
Assimp::Importer importer;
const aiScene* scene = importer.ReadFile(path, aiProcess_Triangulate | aiProcess_FlipUVs);
```
������������һ��Importer�����������ֿռ���Assimp��Ȼ���������ReadFile���������������Ҫһ���ļ�·�����ڶ��������Ǻ���post-processing��ѡ����˿��Լ򵥼����ļ��⣬Assimp�������Ƕ��弸��ѡ����ǿ��Assimpȥ�Ե���������һЩ����ļ���������ͨ������aiProcess_Triangulate�����Ǹ���Assimp���ģ�Ͳ��ǣ�ȫ��������������ɣ�Ӧ��ת�����е�ģ�͵�ԭʼ������״Ϊ�����Ρ�aiProcess_FlipUVs����y�ᷭת�������꣬�ڴ����ʱ���Ǳ���ģ�����ܼǵã�����������̳��У�����˵����OpenGL�����ͼ��ᱻ����y�ᷴת���������СС�ĺ���ѡ���Ϊ���������������һ�ٲ����������õ�ѡ�����£�

aiProcess_GenNormals:���ģ��û�а���������������Ϊÿ�����㴴�����ߡ�
aiProcess_SplitLargeMeshes:�Ѵ������ɼ���С�ĵ��¼����񣬵�����Ⱦ��һ������������������ʱ����ֻ�ܴ���С������ʱ�����á�
aiProcess_OptimizeMeshes:���ϸ�ѡ���෴�����Ѽ���������Ϊһ������������Լ��ٻ��ƺ������õĴ����ķ�ʽ���Ż���
Assimp�ṩ�˺���˵��������Դ������ҵ��������ݡ���ʵ��ͨ��Assimp����һ��ģ�ͳ����򵥡����ѵ���ʹ�÷��صĳ�������Ѽ��ص����ݱ任��һ��Mesh��������顣

������loadModel�����������г���


```c++
void loadModel(string path)
{
    Assimp::Importer import;
    const aiScene* scene = import.ReadFile(path, aiProcess_Triangulate | aiProcess_FlipUVs); 
 
    if(!scene || scene->mFlags == AI_SCENE_FLAGS_INCOMPLETE || !scene->mRootNode) 
    {
        cout << "ERROR::ASSIMP::" << import.GetErrorString() << endl;
        return;
    }
    this->directory = path.substr(0, path.find_last_of('/'));
 
    this->processNode(scene->mRootNode, scene);
}
```
�����Ǽ�����ģ��֮�����Ǽ����Ƿ񳡾��ͳ����ĸ��ڵ�Ϊ�գ��鿴��Щ����е�һ�����������ص������Ƿ�����������������κ�һ����������ͨ����������impoter����GetErrorString�������ش��󱨸档����ͬ�����»�ȡ�ļ���Ŀ¼·����

���ûʲô������������ϣ���������еĳ����ڵ㣬�������Ǵ��ݵ�һ���ڵ㣨���ڵ㣩���ݹ麯��processNode����Ϊÿ���ڵ㣨���ܣ���������ӽڵ㣬����ϣ���ȴ����ڵ��ٴ����ӽڵ㣬�Դ����ơ�����ϵݹ�ṹ���������Ƕ���һ���ݹ麯�����ݹ麯������һ����һЩʲô����֮���ò�ͬ�Ĳ�������������ĺ���������ѭ������ֹͣ��ֱ��һ���ض����������������ǵ�������ض����������еĽڵ㶼������

Ҳ����ǵã�Assimp�Ľṹ��ÿ���ڵ����һ�����񼯺ϵ�������ÿ������ָ��һ���ڳ����������ض�������λ�á�����ϣ����ȡ��Щ������������ȡÿ�����񣬴���ÿ������Ȼ��������Ľڵ���ӽڵ���ͬ���Ĵ���processNode�������������£�


```c++
void processNode(aiNode* node, const aiScene* scene)
{
    // Process all the node's meshes (if any)
    for(GLuint i = 0; i < node->mNumMeshes; i++)
    {
        aiMesh* mesh = scene->mMeshes[node->mMeshes[i]]; 
        this->meshes.push_back(this->processMesh(mesh, scene)); 
    }
    // Then do the same for each of its children
    for(GLuint i = 0; i < node->mNumChildren; i++)
    {
        this->processNode(node->mChildren[i], scene);
    }
}
```
�����������ó�����mMeshes���������ÿ���ڵ�����������Ի�ȡ��Ӧ�����񡣱����ص����񱻴��ݸ�processMesh������������һ������������ǿ��԰���������meshes��list��vector��STL�������ʵ����������ݽṹ���С�

һ�����е����񶼱��������Ǳ��������ӽڵ㣬ͬ������processNode������һ��һ���ڵ㲻��ӵ���κ��ӽڵ㣬�����ͻ�ִֹͣ�С�

����Ķ��߻�ע�⵽�����ǿ��ܻ������Ǵ����κεĽڵ㣬��ѭ�����������е����񣬶�������������������ӵ��¡�������ô����ԭ���ǣ�ʹ�����ֽڵ��ԭʼ���뷨�ǣ�������֮�䶨��һ����-�ӹ�ϵ��ͨ���ݹ������Щ��ϵ�����ǿ������������ض���������Ϊ��������ĸ����ڵ㣩��

�������ϵͳ��һ�����õ������ǣ�������Ҫƽ��һ������������Ҫȷ���������ӣ��ڵ㣩���磬�������񣬷������������̥���񶼽���ƽ�ƣ�ʹ�ø�-�ӹ�ϵ������ϵͳ�����ױ�����������

��������û������ϵͳ���������ۺ�ʱ����Ҫ������������ݽ��ж���Ŀ��ƣ���ͨ����һ�ּ�ֱ��Ƽ�����������Щģ�ͱϾ�����Щ��������Щ�ڵ���Ĺ�ϵ���������������ġ�
��һ�������ϸ��̴̳�����Mesh�࿪ʼ��������Assimp�����ݡ�

 

##��Assimp������

��һ��aiMesh����ת��Ϊһ�������Լ������������󲢲��ѡ�������Ҫ����ȫ���ǻ�ȡÿ��������ص����Բ�����Щ���Դ��浽�����Լ��Ķ���ͨ��processMesh�����Ľṹ����������


```c++
Mesh processMesh(aiMesh* mesh, const aiScene* scene)
{
    vector<Vertex> vertices;
    vector<GLuint> indices;
    vector<Texture> textures;
 
    for(GLuint i = 0; i < mesh->mNumVertices; i++)
    {
        Vertex vertex;
        // Process vertex positions, normals and texture coordinates
        ...
        vertices.push_back(vertex);
    }
    // Process indices
    ...
    // Process material
    if(mesh->mMaterialIndex >= 0)
    {
        ...
    }
 
    return Mesh(vertices, indices, textures);
}
```
����һ�������������������ɣ���ȡ���ж������ݣ���ȡ�������������ȡ��ز������ݡ�����������ݱ�������3����������֮һ���棬һ��Mesh������Щ���ݴ��������ص������ĵ����ߡ�

��ȡ�������ݺܼ򵥣����Ƕ���һ��Vertex�ṹ�壬��ÿ�α��������ǰ�����ṹ����ӵ�Vertices���顣����Ϊ�����������е��ڶඥ��ѭ����ͨ��mesh->mNumVertices��ȡ�����ڱ����Ĺ����У�����ϣ����������������������ṹ�塣ÿ������λ�û�������������


```c++
glm::vec3 vector; 
vector.x = mesh->mVertices[i].x;
vector.y = mesh->mVertices[i].y;
vector.z = mesh->mVertices[i].z; 
vertex.Position = vector;
```
ע�⣬Ϊ�˴���Assimp�����ݣ����Ƕ���һ��vec3��������������Ҫ������ΪAssimpά�����Լ����������ͣ���Щ�����������������ʡ��ַ����ȡ���Щ��������ת����glm����������ʱͨ��Ч�����ѡ�

Assimp�������ǵĶ���λ������mVertices���е�Υ��ֱ����
��Ӧ���ߵĲ�����������������ģ�


```c++
vector.x = mesh->mNormals[i].x;
vector.y = mesh->mNormals[i].y;
vector.z = mesh->mNormals[i].z;
vertex.Normal = vector;
```
��������Ҳ����һ��������Assimp����һ��ģ�͵�ÿ��������8����ͬ���������꣬���ǿ����ò�������������ֻ��ϵ��һ���������ꡣ����Ҳϣ����������Ƿ���İ����������꣨���ܲ���������ˣ�:


```c++
if(mesh->mTextureCoords[0]) // Does the mesh contain texture coordinates?
{
    glm::vec2 vec;
    vec.x = mesh->mTextureCoords[0][i].x; 
    vec.y = mesh->mTextureCoords[0][i].y;
    vertex.TexCoords = vec;
}
else
    vertex.TexCoords = glm::vec2(0.0f, 0.0f);
```
Vertex�ṹ��������ȫ������Ķ�����������ˣ������ܰ����Ƶ�vertices������β����Ҫ��ÿ������Ķ�������ͬ�Ĵ���

 

##����

Assimp�Ľӿڶ���ÿ��������һ�����棨faces��Ϊ��λ�����飬ÿ�������һ��������ͼԪ�������ǵ������У�����aiProcess_Triangulateѡ����������Σ�һ���������������Щ��������������Ҫ���ƵĶ�������������˳���ṩ��ÿ��ͼԪ������������Ǳ��������棬����������������浽indices������������Ҫ��ô����


```c++
for(GLuint i = 0; i < mesh->mNumFaces; i++)
{
    aiFace face = mesh->mFaces[i];
    for(GLuint j = 0; j < face.mNumIndices; j++)
        indices.push_back(face.mIndices[j]);
}
```
�����ⲿѭ��������������������һ��������Ķ������������������������Ҫ����glDrawElements���������ǣ�Ϊ�˽���������ۣ����������ṩһЩϸ�ڣ�����ͬ��ϣ����������Ĳ��ʡ�

 

##����

��ͬ�ڵ㣬һ������ֻ��һ��ָ����ʶ������������ȡ����ʵ�ʵĲ��ʣ�������Ҫ����������mMaterials���顣����Ĳ���������������mMaterialIndex�����У�ͨ�������������ͬ���ܹ�����һ�������Ƿ����һ�����ʣ�


```c++
if(mesh->mMaterialIndex >= 0)
{
    aiMaterial* material = scene->mMaterials[mesh->mMaterialIndex];
    vector<Texture> diffuseMaps = this->loadMaterialTextures(material, 
                                        aiTextureType_DIFFUSE, "texture_diffuse");
    textures.insert(textures.end(), diffuseMaps.begin(), diffuseMaps.end());
    vector<Texture> specularMaps = this->loadMaterialTextures(material, 
                                        aiTextureType_SPECULAR, "texture_specular");
    textures.insert(textures.end(), specularMaps.begin(), specularMaps.end());
}
```
��ô�ȴӳ�����mMaterials�����ȡaimaterial����Ȼ������ϣ�����������diffuse��/��specular����һ�����ʴ�����һ�����飬�������Ϊÿ�����������ṩ����λ�á���ͬ���������Ͷ���aiTextureType_Ϊǰ׺������ʹ��һ������������loadMaterialTextures���Ӳ��ʻ�ȡ���������������һ��Texture�ṹ���������������֮�󴢴���ģ�͵�textures����ĺ��档

loadMaterialTextures�����������и����������͵�����λ�ã���ȡ������ļ�λ�ã�Ȼ�����������������Ϣ���浽Vertex�ṹ�塣��������������


```c++
vector<Texture> loadMaterialTextures(aiMaterial* mat, aiTextureType type, string typeName)
{
    vector<Texture> textures;
    for(GLuint i = 0; i < mat->GetTextureCount(type); i++)
    {
        aiString str;
        mat->GetTexture(type, i, &str);
        Texture texture;
        texture.id = TextureFromFile(str.C_Str(), this->directory);
        texture.type = typeName;
        texture.path = str;
        textures.push_back(texture);
    }
    return textures;
}
```
������ͨ��GetTextureCount������������д�����������ڵõ�����ϣ���õ����������͡�Ȼ������ͨ��GetTexture������ȡÿ��������ļ�λ�ã����λ����aiString���ʹ��档Ȼ������ʹ����һ��������������������Ϊ��TextureFromFile����һ������ʹ��SOIL�������������ID������Բ鿴�������������Ĵ��룬����㲻֪���������Ӧ������д�����Ļ���

ע�⣬���Ǽ��������ļ���ģ��������ͬ��Ŀ¼����ǿ��Լ򵥵���������λ���ַ�����֮ǰ��ȡ��Ŀ¼�ַ�������loadModel�����еõ��ģ����������������·���������ΪʲôGetTexture����ͬ����ҪĿ¼�ַ�������

��Щ�ڻ��������ҵ���ģ��ʹ�þ���·�������ǵ�����λ�þͲ�����ÿ������϶���Ч�ˡ�����������ϣ���ֹ��༭����ļ���ʹ�ñ���·��Ϊ������ʹ�ã�������ܵĻ�����
�����ʹ��Assimp������һ��ģ�͵�ȫ���ˡ�������������ҵ�Model���ȫ����

 

##�ش��Ż�

�������ڻ�û���ꡣ��Ϊ���ǻ�����һ���ش���Ż������ǲ��Ǳ���ģ������������������������������Ӧ�õ����񣻻���˼���Ǹ����������и������ҵ�������Ϊǽ�档�������Ҳ����Ӧ�õ��ذ塢�컨�壬¥�ݣ�����һ�����ӡ�һ��������С���������������Ҫ���ٲ�������ǰ��ʵ����һ���µ��������غ����ɣ���Ϊÿ������ʹ�ã���ʹͬ��������֮ǰ�Ѿ��������˺ü��Ρ����ܿ�ת��Ϊ���ģ�ͼ���ʵ�ֵ�ƿ����

�������Ǵ������һ��СС��΢������ģ�͵Ĵ���ĳɣ��������е��Ѽ�������ȫ�֡��������Ķ����Ƕ�Ҫ�ȼ����������Ƿ��Ѿ������ع��ˡ�������ع��ˣ����Ǿ�ֱ��ʹ�����������������������������ʡ����������Ϊ�˶Ա���������ͬ����Ҫ�������ǵ�·����


```c++
struct Texture {
    GLuint id;
    string type;
    aiString path;  // We store the path of the texture to compare with other textures
};
```
Ȼ�����ǰ����м��ڹ��������浽��һ�������У�������Ϊһ��˽�б���������ģ����Ķ�����


```c++
vector<Texture> textures_loaded;
```
Ȼ����loadMaterialTextures�����У�����ϣ��������·��������texture_loaded�����Աȣ������Ƿ�ǰ����·���������κ�һ���Ƿ���ͬ������ǣ����������������/���ɲ��֣��򵥵�ʹ���Ѽ�������ṹ����Ϊ���������������������ʾ��


```c++
vector<Texture> loadMaterialTextures(aiMaterial* mat, aiTextureType type, string typeName)
{
    vector<Texture> textures;
    for(GLuint i = 0; i < mat->GetTextureCount(type); i++)
    {
        aiString str;
        mat->GetTexture(type, i, &str);
        GLboolean skip = false;
        for(GLuint j = 0; j < textures_loaded.size(); j++)
        {
            if(textures_loaded[j].path == str)
            {
                textures.push_back(textures_loaded[j]);
                skip = true; 
                break;
            }
        }
        if(!skip)
        {   // If texture hasn't been loaded already, load it
            Texture texture;
            texture.id = TextureFromFile(str.C_Str(), this->directory);
            texture.type = typeName;
            texture.path = str;
            textures.push_back(texture);
            this->textures_loaded.push_back(texture);  // Add to loaded textures
        }
    }
    return textures;
}
```
�����������ǲ�������һ��ͨ��ģ�ͼ���ϵͳ��ͬʱ����Ҳ�õ���һ����ʹ���ض��������Ż��汾��

��Щ�汾��Assimp��ʹ�õ��԰��/��ʹ�����IDE�ĵ���ģʽʱ��ģ�ͼ���ģ��ʵ����������ȷ���ڵ�����صú�����ʱ���÷������ٲ��ԡ�
����Դ��������Ż���Model�������Դ���롣

 

##������ģ�͸��!

���ڸ����ǵ���һ����������Ҵ�����ģ�Ϳ���Ч�������������������ģ��㲻�ò����ϣ��������Ҳ�������������Ư��������ͼ�Σ�����Ϊ�Ҳ�������Կ䣬�����һ�ʱ��ʱ�ĸ����������ҽ���������еĻ��ᣬ������ǻ����Crytekԭ��Ĺµ�Σ����Ϸ�е��������ס����ģ�ͱ����Ϊobj��mtl�ļ���mtl����ģ�͵�diffuse��specular�Լ�������ͼ������ὲ����������������ģ�ͣ�ע�⣬���е������ģ���ļ���Ӧ�÷���ͬһ��Ŀ¼���Ա���������

������վ�����صİ汾���޸Ĺ��İ汾��ÿ�������ļ�·���Ѿ��޸ĸ�Ϊ�������Ŀ¼��ԭ������Դ�Ǿ���Ŀ¼��
�����ڴ����У�����һ��Model���󣬰���ģ�͵��ļ�λ�ô��ݸ�����ģ��Ӧ���Զ����أ����û�д���Ļ�������Ϸѭ����ʹ������Draw���������������û�и���Ļ������ã�����ָ�����Ⱦ��������򵥵�һ�С�����㴴�������򵥵���ɫ����������ɫ��ֻ��������diffuse������ɫ���������ȥ���е���������

![](http://www.learnopengl.com/img/model_loading/model_diffuse.png)

����Դ������ҵ����ж����������ɫ����������Դ�롣

����Ҳ���Ա�ø����д������������������Դ������֮ǰ�ӹ��ս̳�ѧ������Ⱦ��ʽ����ϸ߹���ͼ��þ���Ч����

![](http://www.learnopengl.com/img/model_loading/model_lighting.png)

��Ȼ�Ҳ��ò�����������֮ǰ�ù�������Ҳ̫���ˡ�ʹ��Assimp����������������ڻ��������ҵ���ģ�͡�ֻ�к��ٵ���Դ��վ�ṩ���ָ�ʽ�����3Dģ�͸������ء�һ��ע�⣬��Щģ����Ȼ���ܺܺõ����룬����·����Ч�������ָ�ʽAssimp���ܶ���

 

##��ϰ

�����ʹ���������Դ�ؽ��ϸ������𣿷�������ɫ����