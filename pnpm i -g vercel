<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1">
    <title>圣诞树 下雪特效</title>
    <style>
        body { margin: 0; overflow: hidden; background: #001122; }
        #tip { position: absolute; top: 20px; left: 0; right: 0; text-align: center; color: #fff; font-size: 18px; background: rgba(0,0,0,0.6); padding: 12px; z-index: 10; }
        #loader { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #000; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 999; color: #0ff; font-size: 24px; }
        .spinner { width: 60px; height: 60px; border: 5px solid #333; border-top: 5px solid #0ff; border-radius: 50%; animation: spin 1s linear infinite; margin-bottom: 20px; }
        @keyframes spin { to { transform: rotate(360deg); } }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>
    <div id="loader"><div class="spinner"></div><div>加载圣诞树与雪花...</div></div>
    <div id="tip">圣诞快乐！树在旋转，雪花飘落中～点击屏幕看特效 🎄❄️</div>

    <script>
        // 下雪粒子（更多、更自然）
        function createSnow(scene) {
            const snowCount = 12000;
            const geometry = new THREE.BufferGeometry();
            const positions = new Float32Array(snowCount * 3);
            for (let i = 0; i < snowCount; i++) {
                positions[i*3] = (Math.random() - 0.5) * 80;
                positions[i*3+1] = Math.random() * 50 + 10;
                positions[i*3+2] = (Math.random() - 0.5) * 80;
            }
            geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            const material = new THREE.PointsMaterial({color: 0xffffff, size: 0.3, transparent: true});
            const snow = new THREE.Points(geometry, material);
            scene.add(snow);
            return {snow, positions};
        }

        // 场景
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.set(0, 8, 20);
        const renderer = new THREE.WebGLRenderer({antialias: true});
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(window.devicePixelRatio);
        document.body.appendChild(renderer.domElement);

        // 灯光
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
        scene.add(ambientLight);
        const pointLight = new THREE.PointLight(0xffaa00, 2, 100);
        pointLight.position.set(0, 20, 0);
        scene.add(pointLight);

        // 手工圣诞树组
        const treeGroup = new THREE.Group();

        // 树干
        const trunk = new THREE.Mesh(
            new THREE.CylinderGeometry(1, 1.5, 6, 8),
            new THREE.MeshBasicMaterial({color: 0x8B4513})
        );
        trunk.position.y = 3;
        treeGroup.add(trunk);

        // 多层树叶（渐变绿色）
        const layers = [ {r:4, h:5, y:6}, {r:3.2, h:4, y:10}, {r:2.5, h:3.5, y:13}, {r:1.8, h:3, y:15.5} ];
        layers.forEach(l => {
            const cone = new THREE.Mesh(
                new THREE.ConeGeometry(l.r, l.h, 16),
                new THREE.MeshBasicMaterial({color: 0x006400 + Math.random()*0x003300})
            );
            cone.position.y = l.y;
            treeGroup.add(cone);
        });

        // 顶端金星（闪烁）
        const star = new THREE.Mesh(
            new THREE.OctahedronGeometry(1.5),
            new THREE.MeshBasicMaterial({color: 0xffff00})
        );
        star.position.y = 18;
        treeGroup.add(star);

        // 彩灯粒子（随机颜色、闪烁）
        const lightCount = 400;
        const lightGeo = new THREE.BufferGeometry();
        const lightPos = new Float32Array(lightCount * 3);
        const lightCol = new Float32Array(lightCount * 3);
        const colors = [0xff0000, 0x00ff00, 0x0000ff, 0xffff00, 0xff00ff, 0x00ffff];
        for (let i = 0; i < lightCount; i++) {
            const radius = 1 + Math.random() * 3.5;
            const height = 5 + Math.random() * 12;
            const angle = Math.random() * Math.PI * 2;
            lightPos[i*3] = Math.cos(angle) * radius;
            lightPos[i*3+1] = height;
            lightPos[i*3+2] = Math.sin(angle) * radius;
            const c = colors[Math.floor(Math.random() * colors.length)];
            lightCol[i*3] = (c >> 16)/255;
            lightCol[i*3+1] = ((c >> 8)&255)/255;
            lightCol[i*3+2] = (c&255)/255;
        }
        lightGeo.setAttribute('position', new THREE.BufferAttribute(lightPos, 3));
        lightGeo.setAttribute('color', new THREE.BufferAttribute(lightCol, 3));
        const lights = new THREE.Points(lightGeo, new THREE.PointsMaterial({size: 0.5, vertexColors: true, blending: THREE.AdditiveBlending}));
        treeGroup.add(lights);

        scene.add(treeGroup);

        // 下雪
        const {snow, positions} = createSnow(scene);

        // 动画
        function animate() {
            requestAnimationFrame(animate);
            treeGroup.rotation.y += 0.003; // 缓慢旋转
            star.scale.set(1 + Math.sin(Date.now()*0.005)*0.1, 1 + Math.cos(Date.now()*0.005)*0.1, 1); // 星星闪烁

            // 雪花下落
            for (let i = 0; i < positions.length; i += 3) {
                positions[i+1] -= 0.1;
                if (positions[i+1] < -10) positions[i+1] = 50;
            }
            snow.geometry.attributes.position.needsUpdate = true;

            renderer.render(scene, camera);
        }
        animate();

        // 窗口变化
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        // 隐藏加载器
        document.getElementById('loader').remove();
    </script>
</body>
</html>
