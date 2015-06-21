��������JoeyDeVries����[Django(http://bullteacher.com/14-materials.html)]������[http://learnopengl.com](http://learnopengl.com/#!Lighting/Materials)]

����ʵ�����ÿ�������Թ������ͬ�ķ����á��ֱ��մɻ�ƿ���������⡣����һ��ľͷ����͸�һ���Թ�������ͬ�����á�ÿ�������specular�߹�Ҳ�в�ͬ�ķ�Ӧ����Щ���岻��ɢ��ܶ��ȴ�ᷴ��ܶ�⣬�������������һ����С�ĸ߹�㣬��������ɢ��ĸ��࣬�ͻ����һ���뾶����ĸ߹⡣���������Ҫ��OpenGL��ģ�⼸�����͵����壬���Ǳ���Ϊÿ�����嶨��������ԡ�

��ǰ��Ľ̳��У�����ָ��һ�������һ�������ɫ�����������ͼ���������ʹ֮���ambient��specular����Ԫ�ء������������ʱ�����ǿ���Ϊ3�ֹ���Ԫ�أ�ambient��diffuse��specular���շֱ���һ��������ɫ��ͨ��Ϊÿ��Ԫ��ָ��һ����ɫ�������Ѿ����������ɫ������˾��ܵĿ��ơ����ڰ�һ������Ԫ����ӵ���������ɫ�����������Ҫ�����в������ԣ�

```c++
#version 330 core 
struct Material 
{ 
    vec3 ambient; 
    vec3 diffuse; 
    vec3 specular; 
    float shininess; 
}; 
uniform Material material;
```
��������ɫ���У����Ǵ���һ���ṹ�壬����������Ĳ������ԡ�����Ҳ���԰����Ǵ���Ϊ������uniformֵ��������Ϊһ���ṹ����������Ը��������������ȶ���ṹ��Ĳ��֣�Ȼ�������һ��uniform���������½������Ľṹ����Ϊ�������͡�

�������������ģ�����Ϊÿ��Phong���յ�Ԫ�ض�����һ����ɫ������ambient����������������ambient������������巴�����ʲô��ɫ��ͨ�����Ǻ�������ɫ��ͬ����ɫ��diffuse����������������diffuse�������������ɫ��diffuse��ɫ������Ϊ����ambient����һ��������Ҫ�����ɫ��specular�����������õ��������ܵ���specular���Ӱ�����ɫ�����߿����Ƿ���һ�������ض���specular�߹���ɫ�������shininessӰ��specular�߹��ɢ��/�뾶��

���ĸ�Ԫ�ض�����һ������Ĳ��ʣ�ͨ�����������ܹ�ģ��ܶ���ʵ����Ĳ��ʡ�������һ���б�devernay.free.frչʾ�˼��ֲ������ԣ���Щ��������ģ���ⲿ�������ʵ���ʡ������ͼƬչʾ�˼�����ʵ������ʶ����ǵ��������Ӱ�죺
![](http://www.learnopengl.com/img/lighting/materials_real_world.png)
�������������ģ���ȷ��ָ��һ������Ĳ������ԣ��ƺ����Ǹı����������������Եı�����Ч����Ȼ������עĿ�����Ƕ��ڴ������ʵЧ��������������Ҫ���Ӹ��ӵ���״������������һ�������塣������̳̲�������ǻ����۸����ӵ���״��

Ϊһ�����帳����ȷ�Ĳ�����һ�����ѵ����գ�����Ҫ����ʵ��ͷḻ�ľ��飬�������ڴ�������ò��ʶ���������Ļ��������Ǽ������������¡�

��������������ɫ����ʵ��������һ������ϵͳ��


##14.1 ���ò���

������������ɫ���д�����һ��uniform���ʽṹ�壬������������ϣ���ı���ռ�����˳Ӧ�µĲ������ԡ��������в��ʱ������Ǵ����ڽṹ���У����ǿ��Դ�uniform����materialȡ�����ǣ�
```c++
void main()
{    
    // Ambient
    vec3 ambient = lightColor * material.ambient;
  	
    // Diffuse 
    vec3 norm = normalize(Normal);
    vec3 lightDir = normalize(lightPos - FragPos);
    float diff = max(dot(norm, lightDir), 0.0);
    vec3 diffuse = lightColor * (diff * material.diffuse);
    
    // Specular
    vec3 viewDir = normalize(viewPos - FragPos);
    vec3 reflectDir = reflect(-lightDir, norm);  
    float spec = pow(max(dot(viewDir, reflectDir), 0.0), material.shininess);
    vec3 specular = lightColor * (spec * material.specular);  
        
    vec3 result = ambient + diffuse + specular;
    color = vec4(result, 1.0f);
}
```
�������������ģ���ô���ڻ�����в��ʽṹ������ԣ��������Ķ����Ƕ���Ҫ���ǣ����ͨ��������ɫ�İ������������������ɫ�������ÿ���������Զ��������Ƕ�Ӧ�Ĺ���Ԫ�ء�

ͨ�������ʵ���uniform�����ǿ�����Ӧ������������Ĳ��ʡ�������uniformʱ����GLSL�е�һ���ṹ�岻�ᱻ��Ϊ��ʲô�ر�֮����һ���ṹ��ֵ����uniform�����ķ�װ�壬�����������ϣ���������ṹ�壬���Ǿ���Ȼ�������õ�����uniform��������δ��нṹ��������Ϊǰ׺��
```c++
GLint matAmbientLoc = glGetUniformLocation(lightingShader.Program, "material.ambient"); 
GLint matDiffuseLoc = glGetUniformLocation(lightingShader.Program, "material.diffuse"); 
GLint matSpecularLoc = glGetUniformLocation(lightingShader.Program, "material.specular"); 
GLint matShineLoc = glGetUniformLocation(lightingShader.Program, "material.shininess"); 
 
glUniform3f(matAmbientLoc, 1.0f, 0.5f, 0.31f); 
glUniform3f(matDiffuseLoc, 1.0f, 0.5f, 0.31f); 
glUniform3f(matSpecularLoc, 0.5f, 0.5f, 0.5f); 
glUniform1f(matShineLoc, 32.0f);
```
��������ambient��diffuseԪ������ϲ�����������ֵ���ɫ�����������specularԪ��Ϊ�е�������ɫ�����ǲ�ϣ��specularԪ�ض����ָ�������������ǿ�ҵ�Ӱ�졣����ͬ�����ֹ���Ϊ32���������ڿ��Լ򵥵���Ӧ����Ӱ������Ĳ��ʡ�

���г��򣬻�õ����������Ľ����
![](http://www.learnopengl.com/img/lighting/materials_with_material.png)
�������治��ô������


##14.2 �������

�������̫���ˡ����������ԭ����ambient��diffuse��specular��ɫ�����κ�һ����Դȫ���ط��䡣��Դ��ambient��diffuse��specularԪ��ͬʱ���Ų�ͬ�����ȡ�ǰ��Ľ̳̣�����ͨ��ʹ��һ��ǿ��ֵ�ı�ambient��specular���ȵķ�ʽ�������������⣬�������Ϊÿ������Ԫ��ָ�������������������������lightColor��vec3(1.0)�����뿴��������������
```c++
vec3 ambient = vec3(1.0f) * material.ambient; 
vec3 diffuse = vec3(1.0f) * (diff * material.diffuse); 
vec3 specular = vec3(1.0f) * (spec * material.specular);
```
���������ÿ���������Է�����ÿ������Ԫ�ص�ȫ���ȡ���Щvec3(1.0)ֵ���Ը��Զ�����Ӱ��ÿ����Դ����ͨ������������Ҫ�ġ����������ambientԪ����ȫ��Ӱ�������������ɫ������ambientԪ��uyinggai��������ɫ����ô���Ӱ�죬��������Ҫͨ�����ù��ambient����Ϊһ��Сһ���ֵ�ķ�ʽ������ambient��ɫ��
```c++
vec3 result = vec3(0.1f) * material.ambient;
```
���ǿ�����ͬ���ķ�ʽӰ���Դ��diffuse��specular���ȡ��������ǰ��̳������ļ�Ϊ���ƣ������˵�����Ѿ�������һЩ������Ը��Զ�������Ӱ��ÿ������Ԫ�ء�����ϣ��Ϊ������Դ���һЩ����ʽṹ�����ƵĶ�����
```c++
struct Light 
{ 
    vec3 position; 
    vec3 ambient; 
    vec3 diffuse; 
    vec3 specular; 
};
uniform Light light;
```
һ����Դ��ambient��diffuse��specular�ⶼ�в�ͬ�����ȡ�ambient��ͨ������Ϊһ���Ƚϵ͵����ȣ���Ϊ��Ŷ�ǲ�ϣ��ambient��ɫ��Ϊ��������Դ��diffuseԪ��ͨ������Ϊ����ϣ���������е���ɫ��������һ�������İ�ɫ��specularԪ��ͨ������Ϊvec3(1.0f)��ȫ���ȵķ���ȡ�Ҫ��ס��������ͬ���ѹ��λ����ӵ��ṹ���С�

�������uniformһ������ô����Ҫ����������ɫ����
```c++
vec3 ambient = light.ambient * material.ambient; 
vec3 diffuse = light.diffuse * (diff * material.diffuse); 
vec3 specular = light.specular * (spec * material.specular);
```
Ȼ������Ҫ��Ӧ�������ù�����ȣ�
```c++
GLint lightAmbientLoc = glGetUniformLocation(lightingShader.Program, "light.ambient"); 
GLint lightDiffuseLoc = glGetUniformLocation(lightingShader.Program, "light.diffuse"); 
GLint lightSpecularLoc = glGetUniformLocation(lightingShader.Program, "light.specular"); 
 
glUniform3f(lightAmbientLoc, 0.2f, 0.2f, 0.2f); 
glUniform3f(lightDiffuseLoc, 0.5f, 0.5f, 0.5f); // Let's darken the light a bit to fit the scene 
glUniform3f(lightSpecularLoc, 1.0f, 1.0f, 1.0f);
```
���ڣ����ǵ����˹����Ӱ���������еĲ��ʵģ����ǵõ�һ������ǰ��̵̳��Ӿ���������������ȫ������������պͲ��ʣ�
![](http://www.learnopengl.com/img/lighting/materials_light.png)
���ڸı�����������Լ���Щ�������������Ȥ���£�

 

##14.3 ��ͬ�Ĺ����ɫ

ĿǰΪֹ������ֻ��ʹ�ù����ɫȥ�ı����ǵĶ���Ԫ�أ�ѡ����ǴӰ׵��ҵ��ڷ�Χ����ɫ�����Ƕ��е��������ʵ��ɫ��ֻ�����ȣ��������������ڼ򵥵�ȡ���˹�����ԣ����ǿ�������ʱ��ı����ǵ���ɫ����õ�Ч���к���˼������û��ʶ���Ѿ���������ɫ�������ˣ��ı�����ɫ�ܼ򵥣���������������һЩ��Ȥ��Ч����
<video src="http://www.learnopengl.com/video/lighting/materials.mp4" controls="controls">
</video>
�������������ģ���ͬ�Ĺ����ɫ�����Ӱ�����������ɫ��������ڹ����ɫֱ��Ӱ�����巴�����ɫ�������������ɫ�̳̣��������Ӿ������������Ӱ�졣

����sin��glfwGetTime���ı���ambient��diffuse��ɫ�����ǿ�������ʱ�����ż򵥵ĸı�����ɫ��
```c++
glm::vec3 lightColor; lightColor.x = sin(glfwGetTime() * 2.0f); 
lightColor.y = sin(glfwGetTime() * 0.7f); 
lightColor.z = sin(glfwGetTime() * 1.3f); 
 
glm::vec3 diffuseColor = lightColor * glm::vec3(0.5f); // Decrease the influence 
glm::vec3 ambientColor = diffuseColor * glm::vec3(0.2f); // Low influence 
 
glUniform3f(lightAmbientLoc, ambientColor.x, ambientColor.y, ambientColor.z); 
glUniform3f(lightDiffuseLoc, diffuseColor.x, diffuseColor.y, diffuseColor.z);
```
���Ժ�ʵ��ʹ����Щ���պͲ���ֵ��������������Ӱ��ͼ������ġ�����Դ������ҵ�Ӧ�ã���������ɫ����Դ�롣
