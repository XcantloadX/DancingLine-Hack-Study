# 光影

用十六进制编辑器打开`assets\bin\Data\globalgamemanagers`
搜索 `Fastest`，就可以找到 Unity 中 Quality 部分的设置，如图

![](images\image-20211002213746593.png)

框起来的就是画质等级，越往后画质越高
修改步骤：找到最高画质名字，往后数三个字节，修改第四个字节为 `02`，如图
图中红色划线为名称，蓝色为要修改的地方

![image-20211002214519922](images\image-20211002214519922.png)

然后用 dnSpy 打开 `\assets\bin\Data\Managed\Assembly-CSharp.dll`
搜索类 `ColorSet`，修改

```cs
		public string DefaultShader
		{
			get
			{
				//修改前
				//return this.AndroidDefaultShader;
				//修改后
				return this.DefaultShaders[QualitySettings.GetQualityLevel()];
			}
		}
```

搜索类 `MaterialsManager`，修改
```cs
	private void ChangeShaders()
	{

		//修改前
		/*
		foreach (Material material in this.colorMaterials)
		{
			if (!EnvColorEffect.INSTANCE.managedMaterials.Contains(material))
			{
				material.shader = Shader.Find("Aidem/Vertex/Simple Diffuse (Color-mat)");
			}
		}
		foreach (Material material2 in this.fogMaterials)
		{
			if (!EnvColorEffect.INSTANCE.managedMaterials.Contains(material2))
			{
				material2.shader = Shader.Find("Aidem/Vertex/World Space Fog v6");
			}
		}

		*/

		//修改后
		foreach (Material material in this.colorMaterials)
		{
			if (!EnvColorEffect.INSTANCE.managedMaterials.Contains(material))
			{
				material.shader = ((QualitySettings.GetQualityLevel() != 3) ? Shader.Find("Aidem/Vertex/Simple Diffuse (Color-mat)") : Shader.Find("Mobile/Diffuse(Color-mat)"));
			}
		}
		foreach (Material material2 in this.fogMaterials)
		{
			if (!EnvColorEffect.INSTANCE.managedMaterials.Contains(material2))
			{
				material2.shader = ((QualitySettings.GetQualityLevel() != 3) ? Shader.Find("Aidem/Vertex/World Space Fog v6") : Shader.Find("Aidem/Surface/World Space Fog v4"));
			}
		}
	}
```
搜索类`Hero`
```cs
using UnityEngine.Rendering;
//.......
//.......
	public void Start()
		{
			this._trans = base.transform;
			this._rigid = base.GetComponent<Rigidbody>();
			this.mRenderer = base.GetComponent<MeshRenderer>();
			this.backgroundCheckLayerMask = ~LayerMask.GetMask(new string[]
			{
				"HARACTER"
			});
			this.backgroundLayerMask = LayerMask.NameToLayer("BACKGROUND");
			this.backgroundRaycastHit = default(RaycastHit);
			this.backgroundRaycast = new Ray(base.transform.position, Vector3.down);
			this.rayPoints = new Vector3[2];
			this.rayPoints[0] = new Vector3(-0.2f, 0f, 0f);
			this.rayPoints[1] = new Vector3(0.2f, 0f, 0f);
			this.leftDirectionQuaternion = Quaternion.Euler(this.leftDirection);
			this.rightDirectionQuaternion = Quaternion.Euler(this.rightDirection);
			this.straightDirectionQuaternion = Quaternion.Euler(this.straightDirection);
            //下面是新加的
			QualitySettings.shadows = ShadowQuality.All;
			QualitySettings.shadowResolution = ShadowResolution.VeryHigh;
			QualitySettings.shadowCascades = 4;
			QualitySettings.shadowProjection = ShadowProjection.CloseFit;
			QualitySettings.antiAliasing = 8;
			QualitySettings.softParticles = true;
			foreach (Light light in UnityEngine.Object.FindObjectsOfType<Light>())
			{
				light.shadows = LightShadows.Soft;
				light.shadowResolution = LightShadowResolution.FromQualitySettings;
				light.shadowStrength = 1f;
			}
		}
//..............

		public void StartHero()
		{
			if (this.mRenderer != null)
			{
				this.mRenderer.enabled = true;
			}
			this.startTime = Time.time;
			this.started = true;
			this.killedBy = null;
			this.rigid.isKinematic = false;
			if (this.OnHeroStarted != null)
			{
				this.OnHeroStarted.Invoke();
			}
            
            //下面是新加的
            //这些不用改也可以
			GameObject[] array = UnityEngine.Object.FindObjectsOfType<GameObject>();
			try
			{
				foreach (GameObject gameObject in array)
				{
					if (gameObject.name == "Headphones")
					{
						gameObject.GetComponent<MeshRenderer>().material.shader = Shader.Find("Mobile/Diffuse(Color-mat)");
						gameObject.GetComponent<MeshRenderer>().shadowCastingMode = ShadowCastingMode.On;
					}
				}
			}
			catch (Exception)
			{
			}
		}
```
最后打包，签名即可。

# 附录
1. 匹配`//`注释的正则：`//(.*?)\r?\n`，用 VS 打开项目可以批量替换
2. 在[这里](https://androidapksfree.com/dancing-line/dancing-line/old/)可以找到 DL  的旧版本

