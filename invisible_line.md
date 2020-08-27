# 不可视的线
类 LinePool，方法 UseMesh：
```cs
		public void UseMesh(Mesh tail, Mesh ending)
		{
			this.CollectLines(); //这一句可能会有所不同
			for (int i = 0; i < this.lines.Length; i++)
			{
				//修改位置
				this.lines[i].Box.GetComponent<MeshFilter>().sharedMesh = null;
				this.lines[i].Ending.GetComponent<MeshFilter>().sharedMesh = null;
			}
		}
```

类 LineContainer，方法 StartLine：
```cs
		public void StartLine(Transform head, GameCharacter.DIRECTION dir)
		{
			this.startDirection = dir;
			this._head = head;
			this.Started = true;
			this.startPos = base.transform.position;
			Vector3 headLocalPos = this._head.TransformPoint(this.BoxScaleHeadOffset);
			this.ScaleBox(headLocalPos);
			this.paused = false;
            
            //修改位置
			Destroy(this._head.GetComponentInChildren<Renderer>());
			Destroy(this.Box.GetComponent<Renderer>());
		}
```