### USAGE

1. 如何解析 sps/pps/vps
    以解析 sps 为例
    ```golang

    //sps 原始数据，不带 start code(0x00000001)
    var rawsps []byte = []byte{0x67,....}
    
    //step1 创建 BitStream
    bs := codec.NewBitStream(rawsps)

    //step2 解析 sps
    sps := &SPS{}
    sps.Decode(bs)

    ```
2. 获取视频宽高
    以 h264 为例子
    ```golang
    //sps 原始数据，以 startcode 开头 (0x00000001)
    var rawsps []byte = []byte{0x00,0x00,0x00,0x01,0x67,.....}
    w, h := codec.GetH264Resolution(rawsps)
    ```

3. 生成 Extradata
    以 h264 为例子
    ```golang

    //多个 sps 原始数据，以 startcode 开头 (0x00000001)
    var spss [][]byte = [][]byte{
        []byte{0x00,0x00,0x00,0x01,0x67,...},
        []byte{0x00,0x00,0x00,0x01,0x67,...},
        ....
    }

     //多个 pps 原始数据，以 startcode 开头 (0x00000001)
    var ppss [][]byte = [][]byte{
        []byte{0x00,0x00,0x00,0x01,0x68,...},
        []byte{0x00,0x00,0x00,0x01,0x68,...},
        ....
    }
    extranData := codec.CreateH264AVCCExtradata(spss,ppss)
    ```

4. Extradata 转为 Annex-B 格式的 sps,pps
    以 h264 为例子
    ```golang

    //一般从 flv/mp4 格式中获取 extraData
    //解析出来多个 sps,pps, 且 sps pps 都以 startcode 开头
    spss,ppss := codec.CovertExtradata(extraData)
    ```

5. 生成 H265 extrandata
    
    ```golang
    // H265 的 extra data 生成过程稍微复杂一些
    //创建一个 HEVCRecordConfiguration 对象

    hvcc := codec.NewHEVCRecordConfiguration()
    
    //对每一个 sps/pps/vps,调用相应的UpdateSPS,UpdatePPS,UpdateVPS接口
    hvcc.UpdateSPS(sps)
    hvcc.UpdatePPS(pps)
    hvcc.UpdateVPS(vps)

    //调用 Encode 接口生成
    extran := hvcc.Encode()
    ```
6. 获取对应的 sps id/vps id/pps id

    ```golang
    //以 h264 为例子，有四个接口
    //sps 以 startcode 开头
    codec.GetSPSIdWithStartCode(sps)

    //sps2 不以 startcode 开头
    codec.GetSPSId(sps2)

    //pps 以 startcode 开头
    codec.GetPPSIdWithStartCode(pps)

    //pps2 不以 startcode 开头
    codec.GetPPSId(pps2)



    ```
