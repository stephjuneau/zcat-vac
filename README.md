# zcat-vac
Creating and testing DESI-EDR Redshift Value Added Catalogs (VACs).

# ztile

Peculiarity for EDR: one `TILEID` is duplicated with two values of `LASTNIGHT`

# zpix

## Patch file with updated targeting info
Some targeting info changed for a given `TARGETID` during SV1 such as some targets being added to secondary programs. We conducted a query to find what the targeting bits should be (combining all instances with a `BIT_OR` operator). Then we compared this table with the original zpix catalog to identify which rows needed to be patched with updated values. The original query was:

```
SELECT zp.id,zp.targetid,zp.program,zp.survey, BIT_OR(tgt.cmx_target) AS cmx_target,
       BIT_OR(tgt.desi_target) AS desi_target, BIT_OR(tgt.bgs_target) AS bgs_target,
       BIT_OR(tgt.mws_target) AS mws_target,BIT_OR(tgt.sv1_desi_target) AS sv1_desi_target,
       BIT_OR(tgt.sv1_bgs_target) AS sv1_bgs_target,BIT_OR(tgt.sv1_mws_target) AS sv1_mws_target,
       BIT_OR(tgt.sv2_desi_target) AS sv2_desi_target,BIT_OR(tgt.sv2_bgs_target) AS sv2_bgs_target,
       BIT_OR(tgt.sv2_mws_target) AS sv2_mws_target,BIT_OR(tgt.sv3_desi_target) AS sv3_desi_target,
       BIT_OR(tgt.sv3_bgs_target) AS sv3_bgs_target,BIT_OR(tgt.sv3_mws_target) AS sv3_mws_target,
       BIT_OR(tgt.scnd_target) AS scnd_target,BIT_OR(tgt.sv1_scnd_target) AS sv1_scnd_target,
       BIT_OR(tgt.sv2_scnd_target) AS sv2_scnd_target,BIT_OR(tgt.sv3_scnd_target) AS sv3_scnd_target
FROM fuji.zpix AS zp
JOIN (SELECT t.targetid, t.survey, t.program,
             t.cmx_target, t.desi_target, t.bgs_target, t.mws_target,
             t.sv1_desi_target, t.sv1_bgs_target,t.sv1_mws_target,
             t.sv2_desi_target, t.sv2_bgs_target, t.sv2_mws_target,
             t.sv3_desi_target, t.sv3_bgs_target,t.sv3_mws_target,
             t.scnd_target, t.sv1_scnd_target,t.sv2_scnd_target,t.sv3_scnd_target
      FROM fuji.target AS t JOIN fuji.fiberassign as fa
      ON t.targetid=fa.targetid AND t.tileid=fa.tileid) AS tgt
ON (zp.targetid=tgt.targetid AND zp.survey=tgt.survey AND zp.program=tgt.program)
GROUP BY zp.id,zp.targetid,zp.program,zp.survey
```

The query above was run on the original database before the zcat VACs were created. The database (schema) name is `fuji` at NERSC and `desi_edr` at Data Lab.
