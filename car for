<!doctype html>
<html lang="ar">
<head>
  <meta charset="utf-8" />
  <title>🚗 لعبة السباق</title>
  <style>
    body { margin:0; overflow:hidden; background:#000; }
    canvas { display:block; }
    #controls {
      position:fixed; bottom:20px; width:100%; height:120px; 
      display:flex; justify-content:center; align-items:center;
      gap:40px; z-index:5;
    }
    .ctrl-btn {
      background:rgba(0,0,0,0.6); color:white; font-size:22px;
      border-radius:50%; width:60px; height:60px;
      display:flex; align-items:center; justify-content:center;
      user-select:none; touch-action:none;
    }
    .ctrl-btn:active {background:rgba(255,0,0,0.8);}
    #msg {
      position:fixed; top:20px; left:50%; transform:translateX(-50%);
      font-size:24px; font-weight:bold; color:white; background:#000a;
      padding:10px 20px; border-radius:10px; display:none; z-index:6;
      cursor:pointer;
    }
    #speedometer {
      position:fixed; bottom:20px; right:20px;
      font-size:22px; color:white; background:#000a;
      padding:10px 15px; border-radius:10px; z-index:6;
    }
    #startMenu {
      position:fixed; top:0; left:0; width:100%; height:100%;
      background:rgba(0,0,0,0.9); color:white;
      display:flex; flex-direction:column; justify-content:center; align-items:center;
      z-index:10;
    }
    #startMenu h1 { font-size:28px; margin-bottom:20px; }
    #startBtn {
      background:#ff4444; padding:15px 30px; font-size:22px; border-radius:10px;
      cursor:pointer;
    }
  </style>
</head>
<body>
  <div id="startMenu">
    <h1>👋 أهلًا بكم في لعبتنا الجميلة</h1>
    <div id="startBtn">🚀 ابدأ</div>
  </div>
  <div id="msg">💥 اصطدمت! 🔄 اضغط لإرجاع السيارة للبداية</div>
  <div id="controls">
    <div class="ctrl-btn" id="btnLeft">⬅️</div>
    <div class="ctrl-btn" id="btnUp">⬆️</div>
    <div class="ctrl-btn" id="btnDown">⬇️</div>
    <div class="ctrl-btn" id="btnRight">➡️</div>
  </div>
  <div id="speedometer">Speed: 0</div>

  <!-- أصوات -->
  <audio id="engineSound" src="https://cdn.pixabay.com/download/audio/2023/03/21/audio_3d5f0204c6.mp3?filename=car-engine-loop-14499.mp3" loop></audio>
  <audio id="rainSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_27f51b6d4b.mp3?filename=rain-ambient-loop-10408.mp3" loop></audio>

  <script src="https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.min.js"></script>
  <script>
  let scene = new THREE.Scene();
  let camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 8000);
  let renderer = new THREE.WebGLRenderer({antialias:true});
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  // أرضية (شارع رمادي)
  const groundGeo = new THREE.PlaneGeometry(2000,2000);
  const groundMat = new THREE.MeshPhongMaterial({color:0x333333});
  const ground = new THREE.Mesh(groundGeo, groundMat);
  ground.rotation.x = -Math.PI/2;
  scene.add(ground);

  // إضاءة مثل الملاعب
  const stadiumLights = new THREE.PointLight(0xffffff, 1.2, 3000);
  stadiumLights.position.set(0,500,0);
  scene.add(stadiumLights);
  scene.add(new THREE.AmbientLight(0x404040));

  // سيارة اللاعب
  let car = new THREE.Group();
  const bodyGeo = new THREE.BoxGeometry(1.8,0.6,4);
  const bodyMat = new THREE.MeshPhongMaterial({color:0xff2222});
  const body = new THREE.Mesh(bodyGeo,bodyMat);
  body.position.y=0.6;
  car.add(body);

  const glass = new THREE.Mesh(new THREE.BoxGeometry(1.6,0.4,1.5), new THREE.MeshPhongMaterial({color:0x88ccff, transparent:true, opacity:0.6}));
  glass.position.set(0,1,0.2);
  car.add(glass);

  const door = new THREE.Mesh(new THREE.BoxGeometry(0.05,0.6,2), new THREE.MeshPhongMaterial({color:0x222222}));
  door.position.set(0.9,0.6,0);
  car.add(door);

  function makeWheel(x,z){
    let w = new THREE.Mesh(new THREE.CylinderGeometry(0.4,0.4,0.3,16), new THREE.MeshPhongMaterial({color:0x000000}));
    w.rotation.z=Math.PI/2; w.position.set(x,0.4,z); car.add(w);
  }
  makeWheel(0.9,1.5); makeWheel(-0.9,1.5);
  makeWheel(0.9,-1.5); makeWheel(-0.9,-1.5);

  car.position.set(0,0,0);
  scene.add(car);

  // جدران وردية
  const wallMat = new THREE.MeshPhongMaterial({color:0xff55aa});
  let wallN = new THREE.Mesh(new THREE.BoxGeometry(2000,200,10), wallMat); wallN.position.set(0,100,-1000); scene.add(wallN);
  let wallS = new THREE.Mesh(new THREE.BoxGeometry(2000,200,10), wallMat); wallS.position.set(0,100,1000); scene.add(wallS);
  let wallW = new THREE.Mesh(new THREE.BoxGeometry(10,200,2000), wallMat); wallW.position.set(-1000,100,0); scene.add(wallW);
  let wallE = new THREE.Mesh(new THREE.BoxGeometry(10,200,2000), wallMat); wallE.position.set(1000,100,0); scene.add(wallE);

  // مباني (أصفر)
  for(let i=-2000;i<2000;i+=200){
    for(let side of [-30,30]){
      let height = 10+Math.random()*40;
      let buildingGeo = new THREE.BoxGeometry(20,height,20);
      let buildingMat = new THREE.MeshPhongMaterial({color:0xffff00});
      let building = new THREE.Mesh(buildingGeo, buildingMat);
      building.position.set(side, height/2, i);
      scene.add(building);
    }
  }

  // أشباح
  let ghosts=[];
  for(let g=0; g<15; g++){
    let ghostGeo = new THREE.SphereGeometry(1.2,16,16);
    let ghostMat = new THREE.MeshPhongMaterial({color:0xaaaaee, transparent:true, opacity:0.5});
    let ghost = new THREE.Mesh(ghostGeo, ghostMat);
    ghost.position.set((Math.random()-0.5)*1800, 1.5, (Math.random()-0.5)*1800);
    scene.add(ghost);
    ghosts.push(ghost);
  }

  // أعداء
  let enemies=[];
  function spawnEnemy(){
    let e = new THREE.Mesh(new THREE.BoxGeometry(1.8,0.8,3.5), new THREE.MeshPhongMaterial({color:0x00ff00}));
    e.position.set((Math.random()-0.5)*1800,0.5,(Math.random()-0.5)*1800);
    scene.add(e);
    enemies.push(e);
  }
  setInterval(spawnEnemy,5000);

  // مطبات
  let bumps=[];
  for(let x=-900; x<900; x+=100){
    for(let z=-900; z<900; z+=100){
      let bump = new THREE.Mesh(new THREE.CylinderGeometry(2,2,0.5,16), new THREE.MeshPhongMaterial({color:0x444444}));
      bump.position.set(x,0.25,z);
      scene.add(bump);
      bumps.push(bump);
    }
  }

  // تحكم
  let input={up:false,down:false,left:false,right:false};
  function bindBtn(id,dir){
    const b=document.getElementById(id);
    b.addEventListener("touchstart",e=>{e.preventDefault();input[dir]=true;},{passive:false});
    b.addEventListener("mousedown",()=>input[dir]=true);
    const stop=()=>input[dir]=false;
    b.addEventListener("touchend",stop); b.addEventListener("mouseup",stop); b.addEventListener("mouseleave",stop);
  }
  bindBtn("btnUp","up"); bindBtn("btnDown","down"); bindBtn("btnLeft","left"); bindBtn("btnRight","right");

  // فيزياء
  let speed=0, angle=0;
  const maxSpeed=40;
  const accel=0.5;
  const friction=0.96;
  let crashed=false;

  function resetCar(){
    car.position.set(0,0,0);
    car.rotation.set(0,0,0);
    angle=0; speed=0;
    crashed=false;
    document.getElementById("msg").style.display="none";
  }

  function animate(){
    requestAnimationFrame(animate);

    if(!crashed){
      if(input.up && speed<maxSpeed) speed+=accel;
      if(input.down && speed>-maxSpeed/2) speed-=accel;
      if(!input.up && !input.down) speed*=friction;

      if(input.left) angle+=0.05;
      if(input.right) angle-=0.05;

      car.position.x += Math.sin(angle)*speed*0.3;
      car.position.z += Math.cos(angle)*speed*0.3;
      car.rotation.y = angle;

      camera.position.set(
        car.position.x - Math.sin(angle)*6,
        car.position.y + 3,
        car.position.z - Math.cos(angle)*6
      );
      camera.lookAt(car.position.x, car.position.y+1, car.position.z);

      document.getElementById("speedometer").innerText = "Speed: " + Math.abs(speed).toFixed(0);

      for(let b of bumps){
        if(b.position.distanceTo(car.position)<2){ car.position.y=0.3+Math.sin(Date.now()/100)*0.5; }
      }

      for(let g of ghosts){
        if(g.position.distanceTo(car.position)<2){ crashed=true; document.getElementById("msg").style.display="block"; }
      }
      for(let e of enemies){
        if(e.position.distanceTo(car.position)<2){ crashed=true; document.getElementById("msg").style.display="block"; }
      }
      if(car.position.x<=-990||car.position.x>=990||car.position.z<=-990||car.position.z>=990){
        crashed=true; document.getElementById("msg").style.display="block";
      }
    }
    renderer.render(scene,camera);
  }

  // زر إعادة
  document.getElementById("msg").onclick=resetCar;

  // قائمة البداية
  document.getElementById("startBtn").onclick=()=>{
    document.getElementById("startMenu").style.display="none";
    animate();
    document.getElementById("engineSound").play();
    document.getElementById("rainSound").play();
  };

  window.addEventListener("resize",()=>{
    camera.aspect=window.innerWidth/window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });
  </script>
</body>
</html>
