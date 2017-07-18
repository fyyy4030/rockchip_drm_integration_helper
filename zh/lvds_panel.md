#LVDS屏配置

驱动:

    drivers/gpu/drm/rockchip/rockchip_lvds.c

参考配置:

    arch/arm64/boot/dts/rockchip/rk3368-sheep-lvds.dts

文档

    Documentation/devicetree/bindings/video/rockchip-lvds.txt:

    Required properties:
    - compatible: matching the soc type, one of
    	- "rockchip,rk3288-lvds";
    	- "rockchip,rk33xx-lvds";
    
    - reg: physical base address of the controller and length
    	of memory mapped region.
    - reg-names: the name to indicate register. example:
    	- "mipi_lvds_phy": lvds phy register, this's included in the MIPI phy module
    	- "mipi_lvds_ctl": lvds control register, this's included in the MIPI
    		controller module
    - clocks: must include clock specifiers corresponding to entries in the
    	clock-names property.
    - clock-names: must contain "pclk_lvds"
    
    - avdd1v0-supply: regulator phandle for 1.0V analog power
    - avdd1v8-supply: regulator phandle for 1.8V analog power
    - avdd3v3-supply: regulator phandle for 3.3V analog power
    
    - rockchip,grf: phandle to the general register files syscon
    
    - rockchip,data-mapping: should be "vesa" or "jeida",
    	This describes how the color bits are laid out in the
    	serialized LVDS signal.
    - rockchip,data-width : should be <18> or <24>;
    - rockchip,output: should be "rgb", "lvds" or "duallvds",
    	This describes the output face.
    
    Optional properties
    - pinctrl-names: must contain a "default" entry.
    - pinctrl-0: pin control group to be used for this controller.
    - pinctrl-1: pin control group to be used for gpio.
    
    Required nodes:
    
    The lvds has two video ports as described by
    	Documentation/devicetree/bindings/media/video-interfaces.txt.
    Their connections are modeled using the OF graph bindings specified in
    	Documentation/devicetree/bindings/graph.txt.
    
    - video port 0 for the VOP inputs
    - video port 1 for either a panel or subsequent encoder
    
    Example:
    
    For Rockchip RK3288:
    
    	lvds: lvds@ff96c000 {
    		compatible = "rockchip,rk3288-lvds";
    		rockchip,grf = <&grf>;
    		reg = <0xff96c000 0x4000>;
    		clocks = <&cru PCLK_LVDS_PHY>;
    		clock-names = "pclk_lvds";
    		avdd1v0-supply = <&vdd10_lcd>;
    		avdd1v8-supply = <&vcc18_lcd>;
    		avdd3v3-supply = <&vcca_33>;
    		rockchip,data-mapping = "jeida";
    		rockchip,data-width = <24>;
    		rockchip,output = "rgb";
    		ports {
    			#address-cells = <1>;
    			#size-cells = <0>;
    
    			lvds_in: port@0 {
    				reg = <0>;
    
    				lvds_in_vopb: endpoint@0 {
    					reg = <0>;
    					remote-endpoint = <&vopb_out_lvds>;
    				};
    				lvds_in_vopl: endpoint@1 {
    					reg = <1>;
    					remote-endpoint = <&vopl_out_lvds>;
    				};
    			};
    
    			lvds_out: port@1 {
    				reg = <1>;
    
    				lvds_out_panel: endpoint {
    					remote-endpoint = <&panel_in>;
    				};
    			};
    		};
    	};
    
    For Rockchip RK3368:
    
    	lvds: lvds@ff968000 {
    		compatible = "rockchip,rk33xx-lvds";
    		reg = <0x0 0xff968000 0x0 0x4000>, <0x0 0xff9600a0 0x0 0x20>;
    		reg-names = "mipi_lvds_phy", "mipi_lvds_ctl";
    		clocks = <&cru PCLK_DPHYTX0>, <&cru PCLK_MIPI_DSI0>;
    		clock-names = "pclk_lvds", "pclk_lvds_ctl";
    		power-domains = <&power RK3368_PD_VIO>;
    		rockchip,grf = <&grf>;
    		pinctrl-names = "lcdc", "gpio";
    		pinctrl-0 = <&lcdc_lcdc>;
    		pinctrl-1 = <&lcdc_gpio>;
    
    		ports {
    
    		...
    
    		};
    	};