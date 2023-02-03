# kustomize_example
<pre>
作用
    通过将当前目录与依赖的目录的yaml文件进行整合，生成新的yaml文件；通常将通用声明放到base目录中，将需要个性化的声明放到不同的overlay目录，不同overlay目录引用base目录，build生成不同的完整的yaml。
术语与概念
    kustomization
        kustomization可以理解为是kustomize的一个模块单元，一个目录里有kustomizaiton.yaml，那么这个目录就是一个kustomization，不同的kustomization可以相互引用。
    kustomization.yaml
    ｜ apiVersion: kustomize.config.k8s.io/v1beta1
    ｜ kind: Kustomization
    ｜ bases:
    ｜   - ../base
    ｜ patchesStrategicMerge:
    ｜   - deployment_increase_replicas.yaml
    ｜ generatorOptions:
    ｜ disableNameSuffixHash: true
    ｜ configMapGenerator:
    ｜   - name: conf-goods-classification
    ｜ files:
    ｜       - goods-classification.toml
        是kustomize命令编译的入口
        属性
            resources
                用来声明关联的yaml的相对路径，一般在base kustomization中使用
            bases
                指定依赖的base kustomization的目录路径，一般在overlay kustomization中使用
            patchesStrategicMerge
                指定要合并入bases的yaml文件路径，一般在overlay kustomization中使用
            configMapGenerator
                作用
                    用来生成configmap
                name
                    configmap的名字
                files
                    填写一个文件列表的路径，会将这些文件生成一个configmap
            generatorOptions
                作用
                    影响ConfigMapGenerator和SecretGenerator行为
                disableNameSuffixHash: true
                    关闭自动生成的hash串
                labels
                    自动生成labels
                annotations
                    自动生成annotations
            image
            ｜ images:
            ｜ - name: busybox
            ｜   newName: alpine
            ｜   newTag: 3.6
                作用
                    用来修改image的名字或者tag
                name
                    原来的镜像名字
                newName
                    新的镜像名字
                newTag
                    新的tag
            namePrefix
                作用
                    在生成的相关资源名加上一个前缀
            namespace
                作用
                    更新namespace
        参考资料
            https://github.com/kubernetes-sigs/kustomize/blob/master/examples/zh/README.md
    base
        base是一个kustomization,一般用来保存共享的资源声明信息。
            类似面向对象编程的父类
    overlay
        overlay也是一个kustomization,通常依赖于base，overlay声明了与base之间的差异，通过overlay来维护基于base的不同变体，比如开发、QA、生产环境。
            类似与面向对象编程中的重写
    variant
        指将overlay应用于base的结果。
常规目录格式
    myapp
        base
            kustomization.yaml
            ｜ apiVersion: kustomize.config.k8s.io/v1beta1
            ｜ kind: Kustomization
            ｜ commonLabels:
            ｜ app: goods-classification
            ｜ resources:
            ｜   - nginx-deployment.yaml
            ｜   - nginx-service.yaml
            nginx-deployment.yaml
            nginx-service.yaml
        overlays
            dev
                kustomization.yaml
                replication.yaml
            prod
                kustomization.yaml
                replication.yaml
常用操作
    按照规则生成variant
        kustomize build  ./overlays/dev
    按照规则生成variant并在集群执行
        kustomize build ./overlays/dev   |  kubectl apply -f - 
</pre> 
